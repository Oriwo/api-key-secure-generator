# API Key Secure Generator

Generate high-entropy, cryptographically secure API keys directly in your GitHub Actions workflows. This lightweight action uses Python's `secrets` module for maximum security, ensuring your keys are suitable for sensitive applications like authentication tokens, encryption keys, or unique identifiers.

## The Critical Role of Secure API Keys
In the realm of cybersecurity, API keys act as digital gatekeepers, controlling access to sensitive resources. Weak or predictable keys can lead to catastrophic breaches, exposing user data, enabling unauthorized transactions, or compromising entire systems.

From a theoretical standpoint, key security hinges on entropy—the measure of randomness. High-entropy keys resist brute-force attacks, where attackers try every possible combination, and thwart dictionary or rainbow table assaults that exploit common patterns. For instance, a 32-byte key provides approximately 256 bits of entropy, making it computationally infeasible to guess via brute force (requiring 2^256 attempts on average).

Real-world consequences of insecure keys include data theft, financial loss, and reputational damage. By prioritizing cryptographically strong, randomly generated keys, you mitigate these risks and uphold the principles of defense in depth—layering security measures to protect against evolving threats.

## Why Choose This Action?
In today's security-conscious development environment, weak or predictable keys can lead to breaches. This action provides:
- **Unmatched Security**: Leverages OS-level entropy for truly random keys.
- **Flexibility**: Adjust key strength based on your needs.
- **Privacy First**: Keys are never exposed in logs, protecting your secrets.
- **Zero Hassle**: No setup required—just add to your workflow.

## Quick Setup

Integrate secure key generation into your CI/CD pipeline with minimal configuration:

```yaml
- name: Generate Secure API Key
  id: key_step
  uses: frangelbarrera/api-key-secure-generator@v1.1
  with:
    KEY_LENGTH: 64  # Customize length (default: 32 bytes)

- name: Deploy with New Key
  run: deploy --api-key ${{ steps.key_step.outputs.key }}
```

## Configuration Options
- **KEY_LENGTH** (optional): Number of bytes for the key (must be positive integer). Defaults to 32, resulting in ~43 base64 characters.

## What You Get
- **key**: The freshly generated API key, accessible in subsequent steps.

## Under the Hood
The action performs these steps automatically:
1. Validates input parameters for correctness.
2. Calls Python's `secrets.token_urlsafe()` to create a URL-safe random string.
3. Masks the key in workflow logs to prevent leaks.
4. Outputs the key for immediate use in your pipeline.

## Best Practices for Security
- Always mask sensitive data in logs.
- Use strong, unique keys for each service.
- Store keys in GitHub Secrets or encrypted vaults.
- Regularly rotate keys to minimize risk.
- Avoid hardcoding keys in code or configs.

## Advanced Examples

### Simple Key Generation
```yaml
- uses: frangelbarrera/api-key-secure-generator@v1.1
```

### High-Security Keys
```yaml
- uses: frangelbarrera/api-key-secure-generator@v1.1
  with:
    KEY_LENGTH: 128  # For extra-strong keys
```

### Automate Secret Updates
```yaml
- name: Create and Save Key
  id: new_key
  uses: frangelbarrera/api-key-secure-generator@v1.1

- name: Push to Secrets
  uses: actions/github-script@v6
  with:
    script: |
      github.rest.actions.createOrUpdateRepoSecret({
        owner: context.repo.owner,
        repo: context.repo.repo,
        secret_name: 'API_KEY',
        encrypted_value: btoa('${{ steps.new_key.outputs.key }}')
      })
```

## Local Testing
Verify functionality on your machine:
```bash
# Run unit tests
python -m unittest test_run.py

# Build Docker image
docker build -t api-key-gen .
```

## Requirements
- Python 3.11+ (bundled in the container).
- Relies solely on Python's standard library—no extras needed.

## Get Involved
This is an open-source project. Contributions are appreciated! Report bugs, suggest features, or submit pull requests via GitHub.

## License
Released under the MIT License. Full details in LICENSE.

## Resources
- [GitHub Actions Guide](https://docs.github.com/en/actions)
- [Python Secrets Documentation](https://docs.python.org/3/library/secrets.html)
