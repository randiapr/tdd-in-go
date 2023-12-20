# Mocking and Assertion Frameworks

Only using standard package *testing*  can be repetitive and limiting. The [testify](https://github.com/stretchr/testify) and [mockery](https://github.com/vektra/mockery) assertion libraries are two popular choices that can be used to supplement Go’s *testing* package. So we have to import both of libraries into master-service, transaction-service and api-gateway. 

Here we will import testify on each of three service one by one.


```Bash
randi@ottodigital:~/work/tdd-showcase/master-service $ go get github.com/stretchr/testify
go: added github.com/davecgh/go-spew v1.1.1
go: added github.com/pmezard/go-difflib v1.0.0
go: added github.com/stretchr/objx v0.5.0
go: added github.com/stretchr/testify v1.8.4
go: added gopkg.in/yaml.v3 v3.0.1 
```

```Bash
randi@ottodigital:~/work/tdd-showcase/transaction-service $ go get github.com/stretchr/testify
go: added github.com/davecgh/go-spew v1.1.1
go: added github.com/pmezard/go-difflib v1.0.0
go: added github.com/stretchr/objx v0.5.0
go: added github.com/stretchr/testify v1.8.4
go: added gopkg.in/yaml.v3 v3.0.1 
```

```Bash
randi@ottodigital:~/work/tdd-showcase/api-gateway $ go get github.com/stretchr/testify
go: added github.com/davecgh/go-spew v1.1.1
go: added github.com/pmezard/go-difflib v1.0.0
go: added github.com/stretchr/objx v0.5.0
go: added github.com/stretchr/testify v1.8.4
go: added gopkg.in/yaml.v3 v3.0.1 
```

Mockery is a project that creates mock implementations of Golang interfaces. The mocks generated in this project are based off of the github.com/stretchr/testify suite of testing packages. Please refer to this [link](https://vektra.github.io/mockery/latest/installation/) for further information about mockery installation.
