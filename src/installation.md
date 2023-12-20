# Installation
You will need a version of Go later than 1.19 installed on your computer. All code examples have been tested using Go 1.19 on macOS.

|Software|Operating system requirements|
|---|---|
|Go 1.21|Windows, macOS, or Linux|
|PostgreSQL 16|Windows, macOS, or Linux|
|Docker Desktop 4.17|Windows, macOS, or Linux|
|Thunderclient VsCode extension v2.12.1 (optional)|Windows, macOS, or Linux|

## Download the example code files
You can download the example code files for this book from Gitlab at [https://gitlab.pede.id/architecture/tdd-showcase](https://gitlab.pede.id/architecture/tdd-showcase). If there’s an update to the code, it will be updated in the Gitlab repository.

## Text Conventions
There are a number of text conventions used throughout this book.

**Code in Text**: Indicates code words in text, database table names, folder names, filenames, file extensions, pathnames, dummy URLs, user input, and Twitter handles. Here is an example: “The Go toolchain provides the single go test command for running all the tests that we have defined.”

```Go
func(e *ExampleUsecase) Add(x, y float64) float64{
  return x + y
}
```

Any command-line input or output is written as follows:
```Bash
$ go test -run TestAdd ./chapter2/table -v
```