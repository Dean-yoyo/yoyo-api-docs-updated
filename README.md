# Yoyo API Documentation

This repository contains the OpenAPI specification used to generate the Yoyo API documentation using **Redoc**.

## Structure

```
openapi.yaml   → main OpenAPI specification
logo.png       → Yoyo company logo used in documentation
```

## How it works

Redocly reads `openapi.yaml` and renders:

- Left panel → navigation and search
- Center panel → endpoint documentation
- Right panel → request/response examples

## Updating documentation

1. Edit `openapi.yaml`
2. Commit changes
3. Push to the branch connected to Redocly

The documentation site will rebuild automatically.