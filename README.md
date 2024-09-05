# yatl-go

Version: 0.1.0

A Go implementation of 🐙 YATL (Yet Another Tentacle Language) for defining and managing state machines and workflows.

## Introduction

YATL (Yet Another Tentacle Language) is an innovative, octopus-themed markup language designed specifically for defining and managing state machines and workflows. This Go package provides tools to parse, compile, validate, and execute YATL workflows.

## Installation

```bash
go get github.com/usepolvo/yatl-go
```

## Features

- YATL Parser: Converts YATL syntax into an abstract syntax tree (AST)
- YATL Compiler: Transforms the YATL AST into executable code
- YATL Validator: Checks YATL files for syntax and semantic correctness
- YATL Runtime: Executes the compiled YATL workflows
- Trigger System: Supports HTTP, Webhook, Schedule, and Cloud Event triggers

## Usage

Here's a simple example of how to use yatl-go with YATL 1.2 features:

```go
package main

import (
    "fmt"
    "log"

    "github.com/usepolvo/yatl-go/parser"
    "github.com/usepolvo/yatl-go/compiler"
    "github.com/usepolvo/yatl-go/validator"
    "github.com/usepolvo/yatl-go/runtime"
)

func main() {
    // YATL content
    yatlContent := `
name: SimpleWorkflow
description: "A simple workflow example with a schedule trigger"
version: "1.2"
triggers:
  - type: schedule
    cron: "0 * * * *"  # Run every hour
initial_state: Start

states:
  Start:
    type: task
    action: sayHello
    next: Middle

  Middle:
    type: task
    action: sayWorking
    next: End

  End:
    type: task
    action: sayGoodbye
    next: null

actions:
  sayHello:
    description: "Say hello"
    language: go
    code: |
      fmt.Printf("Hello! Triggered at %s\n", context["trigger"].(map[string]interface{})["timestamp"])

  sayWorking:
    description: "Say working"
    language: go
    code: |
      fmt.Println("Working...")

  sayGoodbye:
    description: "Say goodbye"
    language: go
    code: |
      fmt.Println("Goodbye!")

variables:
  dummy: string
`

    // Parse YATL
    parsedYATL, err := parser.Parse(yatlContent)
    if err != nil {
        log.Fatalf("Parsing error: %v", err)
    }

    // Validate YATL
    validator := validator.New()
    if err := validator.Validate(parsedYATL); err != nil {
        log.Fatalf("Validation error: %v", err)
    }

    // Compile YATL
    compiledYATL, err := compiler.Compile(parsedYATL)
    if err != nil {
        log.Fatalf("Compilation error: %v", err)
    }

    // Execute YATL
    runtime := runtime.New(compiledYATL)
    triggerData := map[string]interface{}{
        "type":      "schedule",
        "timestamp": "2023-06-01T12:00:00Z",
    }
    if err := runtime.Execute(triggerData); err != nil {
        log.Fatalf("Execution error: %v", err)
    }
}
```

## Advanced Features

YATL 1.2 supports various state types, control structures, and triggers:

- Task States
- Choice States
- Loop States
- Parallel States
- End States
- Fail States
- Triggers: HTTP, Webhook, Schedule, Cloud Event

For more detailed examples and usage of these features, please refer to the [docs](docs/) directory.

## Development

To set up the development environment:

1. Clone the repository
2. Install dependencies: `go mod download`
3. Run tests: `go test ./...`

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Related Projects

- [usepolvo](https://github.com/usepolvo/usepolvo): The main usepolvo project, an open-source API integration toolkit
- [yatl-python](https://github.com/usepolvo/yatl-python): Python implementation of YATL
- [yatl-node](https://github.com/usepolvo/yatl-node): Node.js implementation of YATL
- [yatl-java](https://github.com/usepolvo/yatl-java): Java implementation of YATL

For more information about YATL and its ecosystem, visit the [YATL Specification](https://github.com/usepolvo/yatl-core/blob/main/specification/yatl-spec.md).
