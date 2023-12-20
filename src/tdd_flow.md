# TDD Flow Againts Test Scenario

<div style="text-align: center;">
<img src="tdd flow.png" alt="tdd_flow" height="700"/>
</div>
<div align="center">Images 6.0 TDD Flow in Go</div>

<br></br>

### Implementing Test Scenario Member
#### Step 1 - Create the test file
*master-service/usecase/member_usecase_test.go*
```Go
package usecase
```
#### Step 2 - Create the source file
*master-service/usecase/member_usecase.go*
```Go
package usecase
```
#### Step 3 - Create the test signature for new functionality
*master-service/usecase/member_usecase_test.go*
```Go
package usecase

type memberUsecaseSuite struct {
}

// TestRegisterMember_Positive test scenario number 1
func (u *memberUsecaseSuite) TestRegisterMember_Positive() {
}
```
#### Step 4 - Write your definitions of your UUT
This step require coding in several files. First, we will create entity and model for member. We can create entity member from previous [class diagram](/class_diagram.html).

*master-service/domain/entity/member.go*
```Go
package entity

import (
	"time"

	"github.com/google/uuid"
)

type Member struct {
	MemberId  uuid.UUID  `json:"member_id"`
	FullName  string     `json:"full_name"`
	BirthDay  string     `json:"birth_day"`
	Address   string     `json:"address"`
	CreatedAt time.Time  `json:"created_at"`
	CreatedBy string     `json:"created_by"`
	UpdatedAt *time.Time `json:"updated_at"`
	UpdatedBy string     `json:"updated_by"`
}
```
This is DTO (Data Transfer Object) member struct from handler to usecase.

*master-service/domain/model/member_model.go*
```Go
package model

type MemberDto struct {
	FullName string `json:"full_name"`
	BirthDay  string `json:"birth_day"`
	Address   string `json:"address"`
}
```
Now, we need member repository to store member to database. First we create member repository interface.

*master-service/domain/repository/member_repository.go*
```Go
package repository

import "master-service/domain/entity"

type MemberRepository interface {
	Create(data *entity.Member) (*entity.Member, error)
}
```

Before we create file connection, we need create a struct to binding configuration from config.yml file.

*master-service/domain/model/config.go*
```Go
type Config struct {
	Env      string
	Version  string
	Host     string
	Port     string
	Postgres struct {
		Host     string
		Port     string
		User     string
		Password string
		Name     string
		Schema   string
		SslMode  string
	}
}
```

After that, we need file connection to database and get configuration from config file. We named it db.go and using [GORM](https://github.com/go-gorm/gorm) library to connect database.

*master-service/infrastructure/persistence/db.go*
```Go
package persistence

import (
	"fmt"
	"master-service/domain/model"

	"gorm.io/driver/postgres"
	"gorm.io/gorm"
)

type Repositories struct {
}

func NewRepositories(cfg model.Config) (*Repositories, error) {
	// init connection postgres
	dsn := fmt.Sprintf("host=%s user=%s password=%s dbname=%s port=%s sslmode=%s TimeZone=Asia/Jakarta",
		cfg.Postgres.Host, cfg.Postgres.User, cfg.Postgres.Password, cfg.Postgres.Name, cfg.Postgres.Port, cfg.Postgres.SslMode)
	_, err := gorm.Open(postgres.Open(dsn))
	if err != nil {
		return nil, err
	}
	return &Repositories{}, nil
}
```

From code above, we create Repositories struct and one method NewRepositories with Config parameter. This method will act as Dependency Injector (DI) for usecase.

After that, we'll implementing MemberRepository interface in persistence package. First, add MemberRepository interface to Repository struct

*master-service/infrastructure/persistence/db.go*
```Go
package persistence

import (
	"fmt"
	"master-service/domain/model"
	"master-service/domain/repository"

	"gorm.io/driver/postgres"
	"gorm.io/gorm"
)

type Repositories struct {
	Member repository.MemberRepository
}
```

And then, we create file Member Repository and implementing MemberRepository interface.

*master-service/infrastructure/persistence/member_repository_impl.go*
```Go
package persistence

import (
	"master-service/domain/entity"
	"master-service/domain/repository"

	"gorm.io/gorm"
)

type memberRepositoryImpl struct {
	db *gorm.DB
}

// Create implements repository.MemberRepository.
func (r *memberRepositoryImpl) Create(data *entity.Member) (*entity.Member, error) {
	return nil, nil
}

func NewMemberRepositoryImpl(db *gorm.DB) repository.MemberRepository {
	return &memberRepositoryImpl{db}
}
```

We already implementing member repository, so we have to call method **NewMemberRepositoryImpl** in db.go .

*master-service/infrastructure/persistence/db.go*
```Go
func NewRepositories(cfg model.Config) (*Repositories, error) {
	// init connection postgres
	dsn := fmt.Sprintf("host=%s user=%s password=%s dbname=%s port=%s sslmode=%s TimeZone=Asia/Jakarta",
		cfg.Postgres.Host, cfg.Postgres.User, cfg.Postgres.Password, cfg.Postgres.Name, cfg.Postgres.Port, cfg.Postgres.SslMode)
	db, err := gorm.Open(postgres.Open(dsn))
	if err != nil {
		return nil, err
	}
	return &Repositories{
		Member: NewMemberRepositoryImpl(db),
	}, nil
}
```

#### Step 5 - Setting up your test scenario
Going back to our test code, 
