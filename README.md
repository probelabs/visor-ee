# Visor Enterprise Edition

Commercial workflows and extensions for [Visor](https://github.com/probelabs/visor).

## Overview

Visor EE provides enterprise-grade workflows for code review, security analysis, and development automation. These workflows are designed for teams requiring advanced features, compliance support, and dedicated enterprise support.

## Features

- **Advanced Code Analysis** - Deep semantic analysis with multi-project support
- **Security Compliance** - SOC2, HIPAA, and PCI-DSS compliance checks
- **Custom Integrations** - Enterprise SSO, LDAP, and audit logging
- **Priority Support** - Dedicated support channel and SLAs

## Installation

Visor EE workflows require a valid enterprise license. Contact [enterprise@probelabs.io](mailto:enterprise@probelabs.io) for licensing.

```yaml
# In your visor config
imports:
  - visor-ee://workflows/code-analysis.yaml
  - visor-ee://workflows/security-compliance.yaml
```

## Directory Structure

```
visor-ee/
├── workflows/           # Enterprise workflow definitions
│   ├── code-analysis/   # Advanced code analysis workflows
│   ├── security/        # Security and compliance workflows
│   └── integrations/    # Enterprise integration workflows
├── tools/               # Custom tool definitions
└── examples/            # Example configurations
```

## License

This software is proprietary and requires a commercial license. See [LICENSE](./LICENSE) for details.

## Support

- Documentation: https://docs.probelabs.io/enterprise
- Support: enterprise@probelabs.io
- Status: https://status.probelabs.io
