# Static Context

## Mastermind Sprig

Many of the utility functions provided come from sprig, a third-party library of Go template functions.
For more information, see [Sprig Function Documentation](https://masterminds.github.io/sprig/) on the sprig website.

## Namespace
```go
func Namespace() string
```
Namespace returns the Kubernetes namespace that the application belongs to.
```yaml
'{{repl Namespace}}'
```

## KubeSeal
```go
func KubeSeal(certData string, namespace string, name string, value string) string
```

## HumanSize
```go
func HumanSize(size interface{}) string
```
HumanSize returns a human-readable approximation of a size in bytes capped at 4 valid numbers (eg. "2.746 MB", "796 KB").
The size must be a integer or floating point number.
```yaml
'{{repl ConfigOption "min_size_bytes" | HumanSize }}'
```

## Now
```go
func Now() string
```
Returns the current timestamp as an RFC3339 formatted string.
```yaml
'{{repl Now }}'
```

## NowFmt
```go
func NowFmt(format string) string
```
Returns the current timestamp as a formatted string.
For information about Go time formatting guidelines, see [Constants](https://golang.org/pkg/time/#pkg-constants) in the Go documentation.
```yaml
'{{repl NowFmt "20060102" }}'
```

## ToLower
```go
func ToLower(stringToAlter string) string
```
Returns the string, in lowercase.
```yaml
'{{repl ConfigOption "company_name" | ToLower }}'
```

## ToUpper
```go
func ToUpper(stringToAlter string) string
```
Returns the string, in uppercase.
```yaml
'{{repl ConfigOption "company_name" | ToUpper }}'
```

## TrimSpace
```go
func TrimSpace(s string) string
```
Trim returns a string with all leading and trailing spaces removed.
```yaml
'{{repl ConfigOption "str_value" | TrimSpace }}'
```

## Trim
```go
func Trim(s string, args ...string) string
```
Trim returns a string with all leading and trailing string contained in the optional args removed (default space).
```yaml
'{{repl ConfigOption "str_value" | Trim " " "." }}'
```

## UrlEncode
```go
func UrlEncode(stringToEncode string) string
```
Returns the string, url encoded.
Equivalent to the `QueryEscape` function within the golang `net/url` library. For more information, see [func QueryEscape](https://godoc.org/net/url#QueryEscape) in the Go documentation.
```yaml
'{{repl ConfigOption "smtp_email" | UrlEncode }}:{{repl ConfigOption "smtp_password" | UrlEncode }}@smtp.example.com:587'
```

## UrlPathEscape
```go
func UrlPathEscape(stringToEncode string) string
```
Returns the string, url *path* encoded.
Equivalent to the `PathEscape` function within the golang `net/url` library. For more information, see [func PathEscape](https://godoc.org/net/url#PathEscape) in the Go documentation.
```yaml
'{{repl ConfigOption "smtp_email" | UrlPathEscape }}:{{repl ConfigOption "smtp_password" | UrlPathEscape }}@smtp.example.com:587'
```

## Base64Encode
```go
func Base64Encode(stringToEncode string) string
```
Returns a Base64 encoded string.
```yaml
'{{repl ConfigOption "name" | Base64Encode }}'
```

## Base64Decode
```go
func Base64Decode(stringToDecode string) string
```
Returns decoded string from a Base64 stored value.
```yaml
'{{repl ConfigOption "base_64_encoded_name" | Base64Decode }}'
```

## Split
```go
func Split(s string, sep string) []string
```
Split slices s into all substrings separated by sep and returns an array of the substrings between those separators.
```yaml
'{{repl Split "A,B,C" "," }}'
```

Combining `Split` and `index`:
Assuming the `github_url` param is set to `https://github.mycorp.internal:3131`, the following would set
`GITHUB_HOSTNAME` to `github.mycorp.internal`.
```yaml
'{{repl index (Split (index (Split (ConfigOption "github_url") "/") 2) ":") 0}}'
```

## RandomString
```go
func RandomString(length uint64, providedCharset ...string) string
```
Returns a random string with the desired length and charset.
Provided charsets must be Perl formatted and match individual characters.
If no charset is provided, `[_A-Za-z0-9]` will be used.

```yaml
'{{repl RandomString 64}}'
```
Or for a total of 64 `a`s and `b`s:
```yaml
'{{repl RandomString 64 "[ab]" }}'
```

Each time that this function is called, the behavior changes based on the `hidden` and `readonly` properties in the Config custom resource. For more information, see [hidden](custom-resource-config#hidden) and [readonly](custom-resource-config#readonly) in _Config_.

- To generate a `RandomString` value that is **persistent** between Config changes, use it in conjunction with the `hidden` property set to `true`. The `value` is not shown in HTML, hence it cannot be modified.
- To generate a `RandomString` value that is **ephemeral** between Config changes, use it in conjunction with the `readonly` property set to `true`. The `value` is shown in HTML but it cannot be modified.
- If `hidden` and `readonly` are not set or set to `false`, the `value` is **persistent** between Config changes but it can also be modified in HTML.
- If both `hidden` and `readonly` are set to `true`, the `value` is not shown in HTML but it is **ephemeral** between Config changes and not modifiable.

## Add
```go
func Add(x interface{}, y interface{}) interface{}
```
Adds x and y.

If at least one of the operands is a floating point number, the result will be a floating point number.

If both operands are integers, the result will be an integer.
```yaml
'{{repl Add (ConfigOption "maximum_users") 1}}'
```

## Sub
```go
func Sub(x interface{}, y interface{}) interface{}
```
Subtracts y from x.

If at least one of the operands is a floating point number, the result will be a floating point number.

If both operands are integers, the result will be an integer.
```yaml
'{{repl Sub (ConfigOption "maximum_users") 1}}'
```

## Mult
```go
func Mult(x interface{}, y interface{}) interface{}
```
Multiplies x and y.

Both operands must be either an integer or a floating point number.

If at least one of the operands is a floating point number, the result will be a floating point number.

If both operands are integers, the result will be an integer.
```yaml
'{{repl Mult (NodePrivateIPAddressAll "DB" "redis" | len) 2}}'
```

If a template function returns a string, the value must be converted to an integer or a floating point number first:
```yaml
'{{repl Mult (ConfigOption "session_cookie_age" | ParseInt) 86400}}'
```

## Div
```go
func Div(x interface{}, y interface{}) interface{}
```
Divides x by y.

If at least one of the operands is a floating point number, the result will be a floating point number.

If both operands are integers, the result will be an integer and will be rounded down.
```yaml
'{{repl Div (ConfigOption "maximum_users") 2.0}}'
```

## ParseBool
```go
func ParseBool(str string) bool
```
ParseBool returns the boolean value represented by the string.
```yaml
'{{repl ConfigOption "str_value" | ParseBool }}'
```

## ParseFloat
```go
func ParseFloat(str string) float64
```
ParseFloat returns the float value represented by the string.
```yaml
'{{repl ConfigOption "str_value" | ParseFloat }}'
```

## ParseInt
```go
func ParseInt(str string, args ...int) int64
```
ParseInt returns the integer value represented by the string with optional base (default 10).
```yaml
'{{repl ConfigOption "str_value" | ParseInt }}'
```

## ParseUint
```go
func ParseUint(str string, args ...int) uint64
```
ParseUint returns the unsigned integer value represented by the string with optional base (default 10).
```yaml
'{{repl ConfigOption "str_value" | ParseUint }}'
```

## TLSCert

**Deprecation Notice**: This function has been superseded in the Replicated app manager 1.26.0 by the sprig crypto functions. For more information, see [Generating TLS certs and keys example](../vendor/packaging-template-functions#generating-tls-certs-and-keys-example) in _Template functions_. For more information about the sprig crypto function, see [Cryptographic and Security Functions](http://masterminds.github.io/sprig/crypto.html) in the sprig documentation.

```go
func TLSCert(certName string, cn string, ips []interface{}, alternateDNS []interface{}, daysValid int) string
```
TLSCert generates and returns a self-signed certificate identified by `certName`.  
The first parameter can be used in the `TLSKey` function to retrieve the matching key.

TLSCert takes the following parameters
- Unique name that identifies the certificate.  
This is a not a part of the returned certificate.
- Subject’s common name (cn)
- Optional list of IPs; may be nil
- Optional list of alternate DNS names; may be nil
- Cert validity duration in days
```yaml
repl{{ TLSCert "my_custom_cert" "foo.com" (list "10.0.0.1" "10.0.0.2") (list "bar.com" "bat.com") 365 }}
```

## TLSKey

**Deprecation Notice**: This function has been superseded in the app manager 1.26.0 by the sprig crypto functions. For more information, see [Generating TLS certs and keys example](../vendor/packaging-template-functions#generating-tls-certs-and-keys-example) in _Template functions_. For more information about the sprig crypto function, see [Cryptographic and Security Functions](http://masterminds.github.io/sprig/crypto.html) in the sprig documentation.

```go
func TLSKey(certName string, cn string, ips []interface{}, alternateDNS []interface{}, daysValid int) string
```
TLSKey returns the key that matches the certificate identified by `certName`.  
The rest of the arguments are the same as in `TLSCert` and, if specified, must have the same values.  
If they are omitted and the certificate with this name does not exist, the function will return an empty string.
```yaml
repl{{ TLSKey "my_custom_cert" "foo.com" (list "10.0.0.1" "10.0.0.2") (list "bar.com" "bat.com") 365 }}
```

## TLSCACert

**Deprecation Notice**: This function has been superseded in the app manager 1.26.0 by the sprig crypto functions. For more information, see [Generating TLS certs and keys example](../vendor/packaging-template-functions#generating-tls-certs-and-keys-example) in _Template functions_. For more information about the sprig crypto function, see [Cryptographic and Security Functions](http://masterminds.github.io/sprig/crypto.html) in the sprig documentation.


```go
func TLSCACert(caName string, daysValid int) string
```
TLSCACert generates and returns a CA certificate that can be used as a CA to sign other certificates.

TLSCACert takes the following parameters
- Subject’s common name (cn)
- Cert validity duration in days
```yaml
repl{{ TLSCACert "foo.com" 365 }}
```

## TLSCertFromCA

**Deprecation Notice**: This function has been superseded in the app manager 1.26.0 by the sprig crypto functions. For more information, see [Generating TLS certs and keys example](../vendor/packaging-template-functions#generating-tls-certs-and-keys-example) in _Template functions_. For more information about the sprig crypto function, see [Cryptographic and Security Functions](http://masterminds.github.io/sprig/crypto.html) in the sprig documentation.

```go
func TLSCertFromCA(caName string, certName string, cn string, ips []interface{}, alternateDNS []interface{}, daysValid int) string
```
TLSCertFromCA generates and returns a certificate signed by the CA identified by `caName`.  
The rest of the arguments are the same as in `TLSCert`.
```yaml
repl{{ TLSCertFromCA "foo.com" "my_custom_cert" "bar.com" (list "10.0.0.1" "10.0.0.2") (list "bar.com" "bat.com") 365 }}
```

## TLSKeyFromCA

**Deprecation Notice**: This function has been superseded in the app manager 1.26.0 by the sprig crypto functions. For more information, see [Generating TLS certs and keys example](../vendor/packaging-template-functions#generating-tls-certs-and-keys-example) in _Template functions_. For more information about the sprig crypto function, see [Cryptographic and Security Functions](http://masterminds.github.io/sprig/crypto.html) in the sprig documentation.

```go
func TLSKeyFromCA(caName string, certName string, cn string, ips []interface{}, alternateDNS []interface{}, daysValid int) string
```
TLSKeyFromCA generates and returns a key that matches the certificate returned by `TLSCertFromCA`.  
The arguments are the same as in `TLSCertFromCA` and their values must match.
```yaml
repl{{ TLSKeyFromCA "foo.com" "my_custom_cert" "bar.com" (list "10.0.0.1" "10.0.0.2") (list "bar.com" "bat.com") 365 }}
```

## IsKurl
```go
func IsKurl() bool
```
IsKurl returns true if running within a kurl-based installation.
```yaml
repl{{ IsKurl }}
```

## Distribution
```go
func Distribution() string
```
Distribution returns the Kubernetes distribution detected. The possible return values are:
* microk8s      
* dockerDesktop
* eks           
* gke           
* digitalOcean  
* openShift     
* kurl          
* aks           
* ibm           
* minikube      
* rke2          
* k3s  

```yaml
repl{{ Distribution }}
```

## NodeCount
```go
func NodeCount() int
```
NodeCount returns the number of nodes detected within the Kubernetes cluster.
```yaml
repl{{ NodeCount }}
```

## HTTPSProxy
```go
func HTTPSProxy() string
```
HTTPSProxy returns the address of the proxy that the Replicated admin console is configured to use.
```yaml
repl{{ HTTPSProxy }}
```

## HTTPProxy
```go
func HTTPProxy() string
```
HTTPProxy returns the address of the proxy that the Replicated admin console is configured to use.
```yaml
repl{{ HTTPProxy }}
```

## NoProxy
```go
func NoProxy() string
```
NoProxy returns the comma-separated list of no-proxy addresses that the admin console is configured to use.
```yaml
repl{{ NoProxy }}
```

## KotsVersion
```go
func KotsVersion() string
```

KotsVersion returns the current KOTS tag/version the admin console is using. **Note**: The app manager is based on the KOTS open source project. The current KOTS version is the same as the current app manager version. The app manager installs the admin console.

```yaml
repl{{ KotsVersion }}
```

`KotsVersion` can be compared to Semantic Versions (SemVers) like follows:
```yaml
repl{{KotsVersion | semverCompare ">= 1.19"}}
```

The above template function will return `true` if `KotsVersion` is greater than `1.19`. **Note**: The app manager is based on the KOTS open source project. The current KOTS version is the same as the current app manager version.

For more complex comparisons, see [Semantic Version Functions](https://masterminds.github.io/sprig/semver.html) in the sprig documentation.

## YamlEscape
```go
func YamlEscape(input string) string
```

YamlEscape returns an escaped and quoted version of the input string, suitable for use within a YAML document.
This can be useful when dealing with user-uploaded files that may include null bytes and other nonprintable characters. For more information about printable characters, see [Character Set](https://yaml.org/spec/1.2.2/#51-character-set) in the YAML documentation.

```yaml
repl{{ ConfigOptionData "my_file_upload" | YamlEscape }}
```
