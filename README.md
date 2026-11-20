# load-secrets-action








Resolves [1Password](https://1password.com) secrets from a JSON map of
env var name to `op://` reference, via the official
[1password/load-secrets-action](https://github.com/1Password/load-secrets-action).


## Usage

```yaml
- uses: rafalmasiarek/load-secrets-action@latest
  env:
    OP_SERVICE_ACCOUNT_TOKEN: ${{ secrets.OP_SERVICE_ACCOUNT_TOKEN }}
    SECRETS_MAP: ${{ vars.SECRETS_MAP }}

- name: Use a loaded secret
  run: echo "${{ env.GPG_PRIVATE_KEY }}" | head -c 0
```


## SECRETS_MAP

A JSON object mapping the env var name you want in later steps to the
`op://` reference that resolves it:

```json
{
  "GPG_PRIVATE_KEY": "op://Infra/GPG/private_key",
  "PASSPHRASE": "op://Infra/GPG/passphrase"
}
```

`SECRETS_MAP` is not itself sensitive - it's a lookup table, not a
value - so it belongs in a GitHub Actions
[variable](https://docs.github.com/en/actions/learn-github-actions/variables)
(`vars.*`), not a secret. The only real secret every caller needs is
`OP_SERVICE_ACCOUNT_TOKEN`.


## Masking

Every resolved value is registered with `core.setSecret()` by the
underlying 1Password action, so GitHub masks it in logs for the rest of
the job - the same as a value from the `secrets` context, even though it
never was one.


## Managing SECRETS_MAP with Terraform

`SECRETS_MAP` is a plain GitHub Actions variable, so it can be set
declaratively wherever your repos are provisioned, e.g. via the
[integrations/github](https://registry.terraform.io/providers/integrations/github)
provider's `github_actions_variable` resource:

```hcl
resource "github_actions_variable" "secrets_map" {
  repository    = "my-repo"
  variable_name = "SECRETS_MAP"
  value = jsonencode({
    GPG_PRIVATE_KEY = "op://Infra/GPG/private_key"
    PASSPHRASE      = "op://Infra/GPG/passphrase"
  })
}
```

The actual values stay in 1Password; only the vault/item references are
ever written to code.







## License

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

```text
The MIT License (MIT)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files, to deal in the Software
without restriction.

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND.

Source: https://opensource.org/licenses/MIT
```


See [LICENSE](LICENSE) for full details.


## Authors

- Rafał Masiarek | [website](https://masiarek.pl) | [email](mailto:rafal@masiarek.pl) | [github](https://github.com/rafalmasiarek)




<!-- references -->

[repo_link]: https://github.com/rafalmasiarek/load-secrets-action
