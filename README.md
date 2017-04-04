Table of Contents
=================

  * [Table of Contents](#table-of-contents)
  * [grok-patterns-sshd](#grok-patterns-sshd)
    * [Usage](#usage)
    * [Creating a pattern](#creating-a-pattern)
      * [Creating a test for the pattern](#creating-a-test-for-the-pattern)
      * [Adding the pattern](#adding-the-pattern)
      * [Testing](#testing)
    * [Variables](#variables)
      * [Reserved variables](#reserved-variables)
    * [Pattern name](#pattern-name)
      * [Reserved pattern names](#reserved-pattern-names)
    * [LICENSE](#license)

# grok-patterns-sshd

`grok` patterns for OpenSSH's `sshd` based on
[whyscream/postfix-grok-patterns](https://github.com/whyscream/postfix-grok-patterns).

## Usage

```sh
git clone git@github.com:reallyenglish/grok-patterns-sshd.git
cd grok-patterns-sshd
bundle install --path vendor/bundle
git submodule update --init --recursive
```

## Creating a pattern

### Creating a test for the pattern

```sh
vim test/test.rb
```

The test file is a YAML file. `pattern` is the pattern name to test, `data` is
the input data for the pattern. `results` is a hash and should have variables
as key, and values that are expected.

```yaml
pattern: ^%{SSHD}$
data: "Accepted publickey for foo from 192.168.1.1 port 38050 ssh2"
results:
  sshd_message: Accepted
  sshd_client_ip: 192.168.1.1
  sshd_login_auth_method: publickey
  sshd_login_user: foo
  sshd_login_port: 38050
  sshd_login_proto: ssh2
```

### Adding the pattern

```sh
vim patterns/sshd.grok
(... add your pattern ...)
```
An example:

```
SSHD_MESSAGE_ACCEPTED Accepted
SSHD_INFO_LOGIN %{SSHD_MESSAGE_ACCEPTED:sshd_message} %{WORD:sshd_login_auth_method} for %{WORD:sshd_login_user} from %{IP:sshd_client_ip} port %{NUMBER:sshd_login_port} %{WORD:sshd_login_proto}
SSHD_INFO %{SSHD_INFO_LOGIN}
SSHD %{SSHD_INFO}
```
For details about `grok` filter, see [the official
documentation](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html).


### Testing

```sh
bundle exec rake test
```

## Variables

A variable must be prefixed with `sshd_`.

### Reserved variables

These are commonly used by the patterns, and reserved.

| Name | Description | Required? |
|------|-------------|-----------|
| `sshd_message`   | the type of message, usually first few strings of the raw message | Required |
| `sshd_client_ip` | IP Address of the client | Optional |
| `sshd_reason`    | the reason of the action taken | Optional |

A variable MAY be assigned by multiple patterns. An example is
`sshd_invalid_login_user`.

## Pattern name

### Reserved pattern names

| Name | Description |
|------|-------------|
| `SSHD_MESSAGE_*` | the value of `sshd_message` |
| `SSHD_INFO` | a regexp of harmless, noisy, or uninteresting log messages, `OR`ed by `|` |
| `SSHD_ERROR` | a regexp of log messages that you should watch |
| `SSHD` | a regexp of `SSHD_INFO` and `SSH_ERROR` `OR`ed by `|` |

Note that `SSHD_INFO` and `SSH_ERROR` have nothing to do with log levels in
`syslog(2)`. They are simply for pattern authors to categorise patterns.

A pattern name must be prefixed with `SSHD_INFO_` or `SSHD_ERROR_`.

Use common postfix in pattern name. If `sshd_message` is defined by
`SSHD_MESSAGE_TIMEOUT`, the pattern should be either `SSHD_INFO_TIMEOUT` or
`SSHD_ERROR_TIMEOUT`, depending on the importance of the event.

Every pattern must be listed in either `SSHD_INFO` or `SSHD_ERROR`.

## LICENSE

```
Copyright 2016 Tom Hendrikx <tom@whyscream.net>
          2017 Tomoyuki Sakurai <tomoyukis@reallyenglish.com>

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice,
this list of conditions and the following disclaimer in the documentation
and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its contributors
may be used to endorse or promote products derived from this software without
specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
```
