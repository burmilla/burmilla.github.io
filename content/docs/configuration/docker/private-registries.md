---
title: Private Registries
weight: 1
---
# Private Registries

When launching services through a [cloud-config](/docs/configuration/#cloud-config), it is sometimes necessary to pull a private image from DockerHub or from a private registry. Authentication for these can be embedded in your cloud-config.

For example, to add authentication for DockerHub:

```yaml
#cloud-config
rancher:
  registry_auths:
    https://index.docker.io/v1/:
      auth: dXNlcm5hbWU6cGFzc3dvcmQ=
```

The `auth` key is generated by base64 encoding a string of the form `username:password`. The `docker login` command can be used to generate an `auth` key. After running the command and authenticating successfully, the key can be found in the `$HOME/.docker/config.json` file.

```json
{
	"auths": {
		"https://index.docker.io/v1/": {
			"auth": "dXNlcm5hbWU6cGFzc3dvcmQ="
		}
	}
}
```

Alternatively, a username and password can be specified directly.

```yaml
#cloud-config
rancher:
  registry_auths:
    https://index.docker.io/v1/:
      username: username
      password: password
```

## Docker Client Authentication

Configuring authentication for the Docker client is not handled by the `registry_auth` key. Instead, the `write_files` directive can be used to write credentials to the standard Docker configuration location.

```yaml
#cloud-config
write_files:
  - path: /home/rancher/.docker/config.json
    permissions: "0755"
    owner: burmilla
    content: |
      {
        "auths": {
          "https://index.docker.io/v1/": {
            "auth": "asdf=",
            "email": "not@val.id"
          }
        }
      }
```

## Certificates for Private Registries

Certificates can be stored in the standard locations (i.e. `/etc/docker/certs.d`) following the [Docker documentation](https://docs.docker.com/registry/insecure). By using the `write_files` directive of the [cloud-config](/docs/configuration/#cloud-config), the certificates can be written directly into `/etc/docker/certs.d`.

```yaml
#cloud-config
write_files:
  - path: /etc/docker/certs.d/myregistrydomain.com:5000/ca.crt
    permissions: "0644"
    owner: root
    content: |
      -----BEGIN CERTIFICATE-----
      MIIDJjCCAg4CCQDLCSjwGXM72TANBgkqhkiG9w0BAQUFADBVMQswCQYDVQQGEwJB
      VTETMBEGA1UECBMKU29tZS1TdGF0ZTEhMB8GA1UEChMYSW50ZXJuZXQgV2lkZ2l0
      cyBQdHkgTHRkMQ4wDAYDVQQDEwVhbGVuYTAeFw0xNTA3MjMwMzUzMDdaFw0xNjA3
      MjIwMzUzMDdaMFUxCzAJBgNVBAYTAkFVMRMwEQYDVQQIEwpTb21lLVN0YXRlMSEw
      HwYDVQQKExhJbnRlcm5ldCBXaWRnaXRzIFB0eSBMdGQxDjAMBgNVBAMTBWFsZW5h
      MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxdVIDGlAySQmighbfNqb
      TtqetENPXjNNq1JasIjGGZdOsmFvNciroNBgCps/HPJphICQwtHpNeKv4+ZuL0Yg
      1FECgW7oo6DOET74swUywtq/2IOeik+i+7skmpu1o9uNC+Fo+twpgHnGAaGk8IFm
      fP5gDgthrWBWlEPTPY1tmPjI2Hepu2hJ28SzdXi1CpjfFYOiWL8cUlvFBdyNqzqT
      uo6M2QCgSX3E1kXLnipRT6jUh0HokhFK4htAQ3hTBmzcxRkgTVZ/D0hA5lAocMKX
      EVP1Tlw0y1ext2ppS1NR9Sg46GP4+ATgT1m3ae7rWjQGuBEB6DyDgyxdEAvmAEH4
      LQIDAQABMA0GCSqGSIb3DQEBBQUAA4IBAQA45V0bnGPhIIkb54Gzjt9jyPJxPVTW
      mwTCP+0jtfLxAor5tFuCERVs8+cLw1wASfu4vH/yHJ/N/CW92yYmtqoGLuTsywJt
      u1+amECJaLyq0pZ5EjHqLjeys9yW728IifDxbQDX0cj7bBjYYzzUXp0DB/dtWb/U
      KdBmT1zYeKWmSxkXDFFSpL/SGKoqx3YLTdcIbgNHwKNMfTgD+wTZ/fvk0CLxye4P
      n/1ZWdSeZPAgjkha5MTUw3o1hjo/0H0ekI4erZFrZnG2N3lDaqDPR8djR+x7Gv6E
      vloANkUoc1pvzvxKoz2HIHUKf+xFT50xppx6wsQZ01pNMSNF0qgc1vvH
      -----END CERTIFICATE-----
```
