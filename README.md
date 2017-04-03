Table of Contents
=================

  * [grok-patterns-sshd](#grok-patterns-sshd)
    * [Usage](#usage)
    * [Creating a pattern](#creating-a-pattern)
      * [Creating a test for the pattern](#creating-a-test-for-the-pattern)
      * [Adding the pattern](#adding-the-pattern)
      * [Testing](#testing)
    * [LICENSE](#license)

# grok-patterns-sshd

`grok` patterns for OpenSSH's `sshd` based on
[whyscream/postfix-grok-patterns](https://github.com/whyscream/postfix-grok-patterns).

## Usage

```sh
git clone git@github.com:reallyenglish/grok-patterns-sshd.git
cd grok-patterns-sshd
bundle install
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
pattern: ^%{SSHD_LOGIN}
data: "Accepted publickey for foo from 192.168.1.1 port 38050 ssh2"
results:
  sshd_login_auth_method: publickey
  sshd_login_user: foo
  sshd_login_ip: 192.168.1.1
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
SSHD_LOGIN Accepted %{WORD:sshd_login_auth_method} for %{WORD:sshd_login_user} from %{IP:sshd_login_ip} port %{NUMBER:sshd_login_port} %{WORD:sshd_login_proto}
```
For details about `grok` filter, see [the official
documentation](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html).


### Testing

```sh
bundle exec rake test
```

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
