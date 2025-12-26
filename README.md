# 🌳 expr - Expression Trees for Go

[![Go Version](https://img.shields.io/badge/Go-%3E%3D%201.21-blue.svg)](https://golang.org/)
[![GoDoc](https://godoc.org/github.com/yourusername/expr?status.svg)](https://godoc.org/github.com/yourusername/expr)
[![Go Report Card](https://goreportcard.com/badge/github.com/yourusername/expr)](https://goreportcard.com/report/github.com/yourusername/expr)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Build Status](https://github.com/yourusername/expr/workflows/CI/badge.svg)](https://github.com/yourusername/expr/actions)

A powerful, type-safe expression tree library for Go that brings C#-style expression trees to the Go ecosystem. Build, manipulate, and compile expressions dynamically at runtime.

## ✨ Features

- 🎯 **Type-Safe Expression Building** - Compile-time and runtime type checking
- 🚀 **High Performance** - Optimized compilation and caching
- 🔧 **Fluent API** - Intuitive, chainable expression building
- 🌊 **LINQ-Style Queries** - Familiar query operations for Go developers
- 🎨 **Visitor Pattern** - Powerful expression tree traversal and transformation
- 📦 **Zero Dependencies** - Pure Go implementation
- 🧪 **Comprehensive Testing** - 95%+ test coverage
- 📚 **Rich Documentation** - Extensive examples and guides

## 🚀 Quick Start

### Installation

```bash
go get github.com/yourusername/expr
```

### Basic Usage

```go
package main

import (
  "fmt"
  "github.com/yourusername/expr"
)

func main() {
  // Create parameters
  x := expr.Parameter("x", expr.Int)
  y := expr.Parameter("y", expr.Int)
  
  // Build expression: (x + y) * 2
  addition := expr.Add(x, y)
  multiplication := expr.Multiply(addition, expr.Constant(2))
  
  // Create lambda: (x, y) => (x + y) * 2
  lambda := expr.Lambda(multiplication, x, y)
  
  // Compile to executable function
  compiled := lambda.Compile()
  
  // Execute with values
  result := compiled(5, 3) // Returns 16
  fmt.Printf("Result: %v\n", result)
  
  // Print expression as string
  fmt.Printf("Expression: %s\n", lambda.String())
  // Output: Expression: (x, y) => ((x + y) * 2)
}
```

## 📖 Table of Contents

- [Installation](#installation)
- [Basic Concepts](#basic-concepts)
- [Expression Types](#expression-types)
- [Building Expressions](#building-expressions)
- [Compilation & Execution](#compilation--execution)
- [LINQ-Style Operations](#linq-style-operations)
- [Advanced Features](#advanced-features)
- [Performance](#performance)
- [Examples](#examples)
- [API Reference](#api-reference)
- [Contributing](#contributing)

## 🎓 Basic Concepts

### Expression Tree

An expression tree is a data structure that represents code in a tree-like format where each node is an expression. This allows you to:

- **Analyze** code structure programmatically
- **Transform** expressions dynamically
- **Compile** expressions to executable functions
- **Serialize** expressions for storage or transmission

### Core Types

```go
// Expression - Base interface for all expressions
type Expression interface {
  Type() reflect.Type
  NodeType() ExpressionType
  String() string
  Accept(visitor Visitor) Expression
}

// ExpressionType - Enumeration of expression types
type ExpressionType int

const (
  ConstantExpr ExpressionType = iota
  ParameterExpr
  BinaryExpr
  UnaryExpr
  LambdaExpr
  CallExpr
  MemberExpr
  ConditionalExpr
)
```

## 🧱 Expression Types

### Constants

```go
// Numeric constants
num := expr.Constant(42)
pi := expr.Constant(3.14159)

// String constants
greeting := expr.Constant("Hello, World!")

// Boolean constants
isTrue := expr.Constant(true)

// Typed constants
typedInt := expr.ConstantT(100, expr.Int64)
```

### Parameters

```go
// Simple parameters
x := expr.Parameter("x", expr.Int)
name := expr.Parameter("name", expr.String)

// Generic parameters
t := expr.ParameterT[int]("value")
```

### Binary Operations

```go
x := expr.Parameter("x", expr.Int)
y := expr.Parameter("y", expr.Int)

// Arithmetic
addition := expr.Add(x, y)           // x + y
subtraction := expr.Subtract(x, y)   // x - y
multiplication := expr.Multiply(x, y) // x * y
division := expr.Divide(x, y)        // x / y
modulo := expr.Modulo(x, y)          // x % y

// Comparison
equal := expr.Equal(x, y)            // x == y
notEqual := expr.NotEqual(x, y)      // x != y
lessThan := expr.LessThan(x, y)      // x < y
greaterThan := expr.GreaterThan(x, y) // x > y

// Logical
and := expr.And(expr.Constant(true), expr.Constant(false)) // true && false
or := expr.Or(expr.Constant(true), expr.Constant(false))   // true || false
```

### Unary Operations

```go
x := expr.Parameter("x", expr.Int)
flag := expr.Parameter("flag", expr.Bool)

// Arithmetic
negation := expr.Negate(x)    // -x
increment := expr.Increment(x) // x++
decrement := expr.Decrement(x) // x--

// Logical
not := expr.Not(flag)         // !flag

// Type conversion
convert := expr.Convert(x, expr.Float64) // float64(x)
```

### Lambda Expressions

```go
// Simple lambda: x => x * 2
x := expr.Parameter("x", expr.Int)
body := expr.Multiply(x, expr.Constant(2))
lambda := expr.Lambda(body, x)

// Multiple parameters: (x, y) => x + y
x := expr.Parameter("x", expr.Int)
y := expr.Parameter("y", expr.Int)
body := expr.Add(x, y)
lambda := expr.Lambda(body, x, y)

// Complex lambda with conditionals
x := expr.Parameter("x", expr.Int)
condition := expr.GreaterThan(x, expr.Constant(0))
ifTrue := expr.Multiply(x, expr.Constant(2))
ifFalse := expr.Constant(0)
body := expr.Condition(condition, ifTrue, ifFalse)
lambda := expr.Lambda(body, x)
```

## 🏗️ Building Expressions

### Fluent API

```go
// Using fluent builder
result := expr.NewBuilder().
  Parameter("x", expr.Int).
  Parameter("y", expr.Int).
  Add("x", "y").
  Multiply(expr.Constant(2)).
  Lambda("x", "y").
  Build()
```

### Factory Functions

```go
// Direct factory functions (recommended)
x := expr.Parameter("x", expr.Int)
y := expr.Parameter("y", expr.Int)

expression := expr.Add(
  expr.Multiply(x, expr.Constant(2)),
  expr.Divide(y, expr.Constant(3)),
)
```

### Complex Expressions

```go
// Mathematical expression: (x^2 + y^2)^0.5
x := expr.Parameter("x", expr.Float64)
y := expr.Parameter("y", expr.Float64)

xSquared := expr.Power(x, expr.Constant(2.0))
ySquared := expr.Power(y, expr.Constant(2.0))
sum := expr.Add(xSquared, ySquared)
result := expr.Power(sum, expr.Constant(0.5))

lambda := expr.Lambda(result, x, y)
compiled := lambda.Compile()

distance := compiled(3.0, 4.0) // Returns 5.0
```

## ⚡ Compilation & Execution

### Basic Compilation

```go
// Create and compile lambda
x := expr.Parameter("x", expr.Int)
lambda := expr.Lambda(expr.Multiply(x, expr.Constant(2)), x)
compiled := lambda.Compile()

// Execute
result := compiled(5) // Returns 10
```

### Compilation Options

```go
// With optimization
options := expr.CompileOptions{
  Optimize: true,
  Cache: true,
  Debug: false,
}

compiled := lambda.CompileWithOptions(options)
```

### Performance Modes

```go
// Interpretation (slower, but works with all expressions)
interpreter := expr.NewInterpreter()
result := interpreter.Execute(lambda, 5)

// Reflection-based compilation (medium performance)
reflectionCompiler := expr.NewReflectionCompiler()
compiled := reflectionCompiler.Compile(lambda)

// Code generation (fastest, requires build-time generation)
codegenCompiler := expr.NewCodegenCompiler()
compiled := codegenCompiler.Compile(lambda)
```

## 🌊 LINQ-Style Operations

```go
import "github.com/yourusername/expr/linq"

// Sample data
numbers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// Where: Filter even numbers
x := expr.Parameter("x", expr.Int)
isEven := expr.Equal(expr.Modulo(x, expr.Constant(2)), expr.Constant(0))
evenNumbers := linq.Where(numbers, expr.Lambda(isEven, x))
// Result: [2, 4, 6, 8, 10]

// Select: Transform to squares
square := expr.Multiply(x, x)
squares := linq.Select(numbers, expr.Lambda(square, x))
// Result: [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

// Chain operations
result := linq.From(numbers).
  Where(expr.Lambda(expr.GreaterThan(x, expr.Constant(5)), x)).
  Select(expr.Lambda(expr.Multiply(x, expr.Constant(2)), x)).
  ToSlice()
// Result: [12, 14, 16, 18, 20]
```

### Complex LINQ Operations

```go
type Person struct {
  Name string
  Age  int
  City string
}

people := []Person{
  {"Alice", 30, "New York"},
  {"Bob", 25, "London"},
  {"Charlie", 35, "New York"},
}

// Group by city and get average age
p := expr.Parameter("p", reflect.TypeOf(Person{}))
city := expr.Member(p, "City")
age := expr.Member(p, "Age")

grouped := linq.From(people).
  GroupBy(expr.Lambda(city, p)).
  Select(func(g linq.Group) interface{} {
      return struct {
          City string
          AverageAge float64
      }{
          City: g.Key.(string),
          AverageAge: linq.From(g.Values).
              Select(expr.Lambda(age, p)).
              Average(),
      }
  }).
  ToSlice()
```

## 🔧 Advanced Features

### Visitor Pattern

```go
// Custom visitor to count nodes
type NodeCounter struct {
  Count int
}

func (nc *NodeCounter) Visit(expr Expression) Expression {
  nc.Count++
  return expr
}

// Count nodes in expression tree
counter := &NodeCounter{}
expr.Accept(counter)
fmt.Printf("Expression has %d nodes\n", counter.Count)
```

### Expression Optimization

```go
// Constant folding optimizer
optimizer := expr.NewOptimizer()
optimized := optimizer.Optimize(expression)

// Before: (2 + 3) * x
// After: 5 * x
```

### Serialization

```go
import "encoding/json"

// Serialize expression to JSON
lambda := expr.Lambda(expr.Add(x, y), x, y)
jsonData, err := json.Marshal(lambda)

// Deserialize from JSON
var restored expr.LambdaExpression
err = json.Unmarshal(jsonData, &restored)
```

### Custom Expression Types

```go
// Define custom expression type
type CustomExpression struct {
  expr.BaseExpression
  Value string
}

func (ce *CustomExpression) NodeType() expr.ExpressionType {
  return expr.CustomExpr
}

func (ce *CustomExpression) Accept(visitor expr.Visitor) expr.Expression {
  return visitor.VisitCustom(ce)
}
```

## 🚀 Performance

### Benchmarks

```
BenchmarkConstantExpression-8       100000000    10.2 ns/op    0 B/op    0 allocs/op
BenchmarkBinaryExpression-8         50000000     28.4 ns/op    0 B/op    0 allocs/op
BenchmarkLambdaCompilation-8        1000000      1542 ns/op    256 B/op  8 allocs/op
BenchmarkCompiledExecution-8        200000000    8.15 ns/op    0 B/op    0 allocs/op
BenchmarkReflectionExecution-8      5000000      312 ns/op     48 B/op   3 allocs/op
```

### Performance Tips

1. **Compile Once, Execute Many**: Compilation is expensive, execution is fast
2. **Use Caching**: Enable compilation caching for repeated expressions
3. **Optimize Expressions**: Use the optimizer for constant folding
4. **Choose Right Compiler**: Code generation > Reflection > Interpretation

```go
// Good: Compile once, use many times
compiled := lambda.Compile()
for i := 0; i < 1000000; i++ {
  result := compiled(i)
}

// Bad: Compiling in loop
for i := 0; i < 1000000; i++ {
  compiled := lambda.Compile()
  result := compiled(i)
}
```

## 📚 Examples

### Mathematical Calculator

```go
func CreateCalculator() func(string, float64, float64) float64 {
  x := expr.Parameter("x", expr.Float64)
  y := expr.Parameter("y", expr.Float64)
  
  operations := map[string]expr.Expression{
      "+": expr.Add(x, y),
      "-": expr.Subtract(x, y),
      "*": expr.Multiply(x, y),
      "/": expr.Divide(x, y),
      "^": expr.Power(x, y),
  }
  
  compiled := make(map[string]func(float64, float64) float64)
  for op, expr := range operations {
      lambda := expr.Lambda(expr, x, y)
      compiled[op] = lambda.Compile().(func(float64, float64) float64)
  }
  
  return func(operation string, a, b float64) float64 {
      if fn, exists := compiled[operation]; exists {
          return fn(a, b)
      }
      panic("Unknown operation: " + operation)
  }
}

// Usage
calc := CreateCalculator()
result := calc("+", 10, 5) // Returns 15
```

### Dynamic Filtering

```go
type Product struct {
  Name     string
  Price    float64
  Category string
  InStock  bool
}

func CreateProductFilter(filterExpr string) func([]Product) []Product {
  // Parse filter expression (simplified)
  p := expr.Parameter("p", reflect.TypeOf(Product{}))
  
  var condition expr.Expression
  switch filterExpr {
  case "cheap":
      condition = expr.LessThan(expr.Member(p, "Price"), expr.Constant(10.0))
  case "expensive":
      condition = expr.GreaterThan(expr.Member(p, "Price"), expr.Constant(100.0))
  case "in_stock":
      condition = expr.Member(p, "InStock")
  }
  
  lambda := expr.Lambda(condition, p)
  compiled := lambda.Compile()
  
  return func(products []Product) []Product {
      var result []Product
      for _, product := range products {
          if compiled(product).(bool) {
              result = append(result, product)
          }
      }
      return result
  }
}
```

### Expression Builder DSL

```go
type ExpressionBuilder struct {
  stack []expr.Expression
}

func NewExpressionBuilder() *ExpressionBuilder {
  return &ExpressionBuilder{}
}

func (eb *ExpressionBuilder) Push(value interface{}) *ExpressionBuilder {
  eb.stack = append(eb.stack, expr.Constant(value))
  return eb
}

func (eb *ExpressionBuilder) Param(name string, typ reflect.Type) *ExpressionBuilder {
  eb.stack = append(eb.stack, expr.Parameter(name, typ))
  return eb
}

func (eb *ExpressionBuilder) Add() *ExpressionBuilder {
  if len(eb.stack) < 2 {
      panic("Not enough operands")
  }
  right := eb.stack[len(eb.stack)-1]
  left := eb.stack[len(eb.stack)-2]
  eb.stack = eb.stack[:len(eb.stack)-2]
  eb.stack = append(eb.stack, expr.Add(left, right))
  return eb
}

func (eb *ExpressionBuilder) Build() expr.Expression {
  if len(eb.stack) != 1 {
      panic("Invalid expression")
  }
  return eb.stack[0]
}

// Usage: Build expression (x + 5) * 2
result := NewExpressionBuilder().
  Param("x", expr.Int).
  Push(5).
  Add().
  Push(2).
  Multiply().
  Build()
```

## 📖 API Reference

### Core Functions

```go
// Constants
func Constant(value interface{}) Expression
func ConstantT[T any](value T) Expression

// Parameters
func Parameter(name string, typ reflect.Type) ParameterExpression
func ParameterT[T any](name string) ParameterExpression

// Binary Operations
func Add(left, right Expression) BinaryExpression
func Subtract(left, right Expression) BinaryExpression
func Multiply(left, right Expression) BinaryExpression
func Divide(left, right Expression) BinaryExpression
func Modulo(left, right Expression) BinaryExpression
func Power(left, right Expression) BinaryExpression

// Comparison
func Equal(left, right Expression) BinaryExpression
func NotEqual(left, right Expression) BinaryExpression
func LessThan(left, right Expression) BinaryExpression
func LessThanOrEqual(left, right Expression) BinaryExpression
func GreaterThan(left, right Expression) BinaryExpression
func GreaterThanOrEqual(left, right Expression) BinaryExpression

// Logical
func And(left, right Expression) BinaryExpression
func Or(left, right Expression) BinaryExpression
func Not(operand Expression) UnaryExpression

// Unary Operations
func Negate(operand Expression) UnaryExpression
func Convert(operand Expression, targetType reflect.Type) UnaryExpression

// Lambda
func Lambda(body Expression, parameters ...ParameterExpression) LambdaExpression

// Member Access
func Member(expression Expression, memberName string) MemberExpression
func Property(expression Expression, propertyName string) MemberExpression
func Field(expression Expression, fieldName string) MemberExpression

// Method Calls
func Call(instance Expression, methodName string, arguments ...Expression) CallExpression
func StaticCall(typ reflect.Type, methodName string, arguments ...Expression) CallExpression

// Conditionals
func Condition(test, ifTrue, ifFalse Expression) ConditionalExpression
func IfThen(test, ifTrue Expression) ConditionalExpression
func IfThenElse(test, ifTrue, ifFalse Expression) ConditionalExpression
```

### Compilation

```go
type CompileOptions struct {
  Optimize bool
  Cache    bool
  Debug    bool
}

type LambdaExpression interface {
  Expression
  Compile() interface{}
  CompileWithOptions(options CompileOptions) interface{}
}
```

### Visitors

```go
type Visitor interface {
  Visit(expr Expression) Expression
  VisitBinary(expr BinaryExpression) Expression
  VisitUnary(expr UnaryExpression) Expression
  VisitConstant(expr ConstantExpression) Expression
  VisitParameter(expr ParameterExpression) Expression
  VisitLambda(expr LambdaExpression) Expression
  VisitMember(expr MemberExpression) Expression
  VisitCall(expr CallExpression) Expression
  VisitConditional(expr ConditionalExpression) Expression
}
```

## 🛠️ Development

### Building from Source

```bash
git clone https://github.com/yourusername/expr.git
cd expr
make build
```

### Running Tests

```bash
make test          # Run all tests
make test-verbose  # Run with verbose output
make bench         # Run benchmarks
make coverage      # Generate coverage report
```

### Code Quality

```bash
make lint          # Run linter
make fmt           # Format code
make vet           # Run go vet
```

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

### Development Setup

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Make your changes
4. Add tests for your changes
5. Run the test suite: `make test`
6. Commit your changes: `git commit -am 'Add amazing feature'`
7. Push to the branch: `git push origin feature/amazing-feature`
8. Open a Pull Request

### Code Style

- Follow standard Go formatting (`gofmt`)
- Write comprehensive tests
- Document public APIs
- Use meaningful variable names
- Keep functions focused and small

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Inspired by C# Expression Trees
- Thanks to the Go community for feedback and contributions
- Special thanks to contributors who helped shape this library

## 📞 Support

- 📖 [Documentation](https://pkg.go.dev/github.com/yourusername/expr)
- 🐛 [Issue Tracker](https://github.com/yourusername/expr/issues)
- 💬 [Discussions](https://github.com/yourusername/expr/discussions)
- 📧 [Email Support](mailto:support@yourproject.com)

---

**Made with ❤️ for the Go community**