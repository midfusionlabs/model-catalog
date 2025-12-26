# Midfusion Model Catalog

Community-maintained model catalog for [Midfusion AI Gateway](https://github.com/midfusionlabs/portalis).

This repository contains model definitions for various AI providers that can be used with Midfusion gateways.

## Structure

```
├── manifest.yaml          # Catalog manifest with metadata
├── version.txt            # Current catalog version
├── changelog.yaml         # Version history and changes
└── providers/             # Provider-specific model definitions
    ├── openai/
    ├── anthropic/
    └── ...
```

## Usage

Midfusion gateways automatically sync with this catalog to get the latest model definitions.

## Contributing

Contributions are welcome! Please submit a pull request with your model additions or updates.

## License

MIT License - see [LICENSE](LICENSE) for details.
