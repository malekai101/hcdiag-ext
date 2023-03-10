# hcdiag-ext

Modified hcdiag configuration for use by HashiCorp with Customers.

## Install the hcdiag binary

- Download [hcdiag v0.5.0](https://releases.hashicorp.com/hcdiag/0.5.0/) manually and install on your PATH _or_ use a package manager:
```sh
# Example RHEL package install steps
yum install -y yum-utils git jq unzip ca-certificates
yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
yum install hcdiag-0.5.0-1 -y

# Example Debian package install steps
curl -fsSL https://apt.releases.hashicorp.com/gpg > /tmp/hashicorp.key
apt-key add < /tmp/hashicorp.key
apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
apt-get update --fix-missing
apt-get install hcdiag="0.5.0-1" --yes
```

## Install hcdiag-ext configuration

- If sufficient access exists, download and unpack the [hcdiag-ext v0.4.1 release package](https://github.com/hashicorp/hcdiag-ext/archive/refs/tags/v0.4.1.zip)[^1] to the target instance(s):
```sh
curl -#Lk https://github.com/hashicorp/hcdiag-ext/archive/refs/tags/v0.4.1.zip -o hcdiag-ext-0.4.1.zip
unzip hcdiag-ext-0.4.1.zip
```

- If the target instance(s) are [air gapped](https://en.wikipedia.org/wiki/Air_gap_(networking)), run the above commands through your web proxy and then copy the relevant hcl files to the target instance(s) so hcdiag can access them prior to execution.
- _On the target instance(s)_, export the necessary environment variables so hcdiag can query the product:
  - For Vault Enterprise, the `VAULT_TOKEN` and `VAULT_ADDR` environment variables must be set
    - An example policy for hcdiag-ext (`hcdiag_vault_policy.hcl`) is contained in this release to limit the scope hcdiag has within Vault
  - For Terraform Enterprise, the `TFE_TOKEN` and `TFE_HTTP_ADDR` environment variables must be set
  - For Consul Enterprise, the `CONSUL_TOKEN` and `CONSUL_HTTP_ADDR` environment variables must be set

## Run hcdiag with the hcdiag-ext configuration

1. hcdiag is best run by a superuser account or the account running the respective product; current hcdiag-ext configurations do not make hcdiag attempt to run commands which require root access, but privileged access to the product API(s) is required to provide complete results.
1. Run hcdiag with specific configuration:
    - Vault Enterprise: `hcdiag run -vault -config /path/to/hcdiag_vault.hcl`
    - Terraform Enterprise: `hcdiag run -terraform-ent -config /path/to/hcdiag_terraform.hcl`
    - Consul Enterprise: `hcdiag run -consul -config /path/to/hcdiag_consul.hcl`
1. Submit the bundle to HashiCorp via the HashiCorp SendSafely portal link shared by your HashiCorp contact.

[^1]: SHA256SUM: 62a573b5cffd76fdd1c082b69cd328cd7f0d28e09477546a02fb19235eaeb702
