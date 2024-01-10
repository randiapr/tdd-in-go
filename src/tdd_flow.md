# TDD Flow Againts Test Scenario

<div style="text-align: center;">
<img src="tdd flow.png" alt="tdd_flow" height="700"/>
</div>
<div align="center">Images 6.0 TDD Flow in Go</div>

<br></br>

### Implementing Test Scenario Member
#### Step 1 - Create the test files for usecase and repository module
*master-service/app/usecase/member_usecase_test.go*
```Go
package usecase

type memberUsecaseSuite struct {	
}
```

*master-service/app/infrastructure/persistence/member_repository_impl_test.go*
```Go
package persistence

type memberRepositorySuite struct {
}
```
#### Step 2 - Create the source files for usecase and repository module
*master-service/app/usecase/member_usecase.go*
```Go
package usecase

type memberUsecaseImpl struct {
}

type MemberUsecase interface {
}

func NewMemberUsecaseImpl() MemberUsecase {
	return &memberUsecaseImpl{}
}
```

*master-service/app/infrastructure/persistence/member_repository_impl.go*
```Go
package persistence

type memberRepositoryImpl struct {
}
```


#### Step 3 - Create the test signature for new functionality
*master-service/app/usecase/member_usecase_test.go*
```Go
package usecase

type memberUsecaseSuite struct {
}

// TestRegisterMember_Positive test scenario number 1
func (u *memberUsecaseSuite) TestRegisterMember_Positive() {
}
```

*master-service/app/infrastructure/persistence/member_repository_impl_test.go*
```Go
package persistence

type memberRepositorySuite struct {
}

func (s *memberRepositorySuite) TestCreateMember_Positive() {
}

```
#### Step 4 - Write your definitions of your UUT
This step require coding in several files. First, we will create entity and model for member. We can create entity member from previous [class diagram](/class_diagram.html).

*master-service/app/domain/entity/member.go*
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

*master-service/app/domain/model/member_model.go*
```Go
package model

type MemberDto struct {
	FullName string `json:"full_name"`
	BirthDay  string `json:"birth_day"`
	Address   string `json:"address"`
}
```
Now, we need member repository interface to store member to database and add to member usecase as dependency. First we create member repository interface.

*master-service/app/domain/repository/member_repository.go*
```Go
package repository

import "master-service/app/domain/entity"

type MemberRepository interface {
	Create(data *entity.Member) (*entity.Member, error)
}
```

*master-service/app/usecase/member_usecase.go*
```Go
package usecase

import (
	"master-service/app/domain/repository"
)

type memberUsecaseImpl struct {
	memberRepo repository.MemberRepository
}
```

Before we create file connection, we need create a struct to binding configuration from config.yml file.

*master-service/config.development.yml*
```yaml
env: development
version: 0.0.1
host: localhost
port: 3027
postgres:
  host: localhost
  port: 5432
  user: randi
  password: mystrongpassword
  name: master
```

*master-service/pkg/util/config.go*
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

*master-service/app/infrastructure/persistence/db.go*
```Go
package persistence

import (
	"fmt"
	"master-service/app/domain/model"

	"gorm.io/driver/postgres"
	"gorm.io/gorm"
)

type Repositories struct {
}

// This method will act as Dependency Injector (DI) for usecase.
func NewRepositories() (*Repositories, error) {
	// init connection postgres
	dsn := fmt.Sprintf("host=%s user=%s password=%s dbname=%s port=%s "+
		"sslmode=%s TimeZone=Asia/Jakarta",
		util.Config.Postgres.Host, util.Config.Postgres.User,
		util.Config.Postgres.Password, util.Config.Postgres.Name,
		util.Config.Postgres.Port, util.Config.Postgres.SslMode)
	_, err := gorm.Open(postgres.Open(dsn))
	if err != nil {
		return nil, err
	}
	return &Repositories{}, nil
}
```

From code above, we create Repositories struct and one method NewRepositories with Config parameter. This method will act as Dependency Injector (DI) for usecase.

After that, we'll implementing MemberRepository interface in persistence package. First, add MemberRepository interface to Repository struct

*master-service/app/infrastructure/persistence/db.go*
```Go
package persistence

import (
	"fmt"
	"master-service/app/domain/model"
	"master-service/app/domain/repository"

	"gorm.io/driver/postgres"
	"gorm.io/gorm"
)

type Repositories struct {
	Member repository.MemberRepository
}
```

And then, we will implementing MemberRepository interface from domain module in persistence module with expected result is failed.

*master-service/app/infrastructure/persistence/member_repository_impl.go*
```Go
package persistence

import (
	"master-service/app/domain/entity"
	"master-service/app/domain/repository"

	"gorm.io/gorm"
)

type memberRepositoryImpl struct {
	db *gorm.DB
}

// Create implements repository.MemberRepository.
func (r *memberRepositoryImpl) Create(data *entity.Member) (*entity.Member, error) {
	return nil, errors.New("unimplemented")
}

func NewMemberRepositoryImpl(db *gorm.DB) repository.MemberRepository {
	return &memberRepositoryImpl{db}
}
```

We already implementing member repository, so we have to call method **NewMemberRepositoryImpl** in db.go .

*master-service/app/infrastructure/persistence/db.go*
```Go
func NewRepositories() (*Repositories, error) {
	// init connection postgres
	dsn := fmt.Sprintf("host=%s user=%s password=%s dbname=%s port=%s "+
		"sslmode=%s TimeZone=Asia/Jakarta",
		util.Config.Postgres.Host, util.Config.Postgres.User,
		util.Config.Postgres.Password, util.Config.Postgres.Name,
		util.Config.Postgres.Port, util.Config.Postgres.SslMode)
	db, err := gorm.Open(postgres.Open(dsn))
	if err != nil {
		return nil, err
	}
	return &Repositories{
		Member: NewMemberRepositoryImpl(db),
	}, nil
}
```

We have to create mock for MemberRepository before we move to usecase.
```Bash
randi@ottodigital:~/work/tdd-showcase/master-service/app/domain/repository 
$ mockery --name=MemberRepository --filename=member_repository_mock.go    
08 Jan 24 07:11 WIB INF couldn't read any config file version=v2.39.1
08 Jan 24 07:11 WIB INF Starting mockery dry-run=false version=v2.39.1
08 Jan 24 07:11 WIB INF Using config:  dry-run=false version=v2.39.1
08 Jan 24 07:11 WIB WRN DEPRECATION: use of the packages config will be the only way to generate mocks in v3. Please migrate your config to use the packages feature. dry-run=false migration=https://vektra.github.io/mockery/v2.39/migrating_to_packages/ url=https://vektra.github.io/mockery/v2.39/features/#packages-configuration version=v2.39.1
08 Jan 24 07:11 WIB INF Walking dry-run=false version=v2.39.1
08 Jan 24 07:11 WIB INF Generating mock dry-run=false interface=MemberRepository qualified-name=master-service/app/domain/repository version=v2.39.1
08 Jan 24 07:11 WIB INF writing mock to file dry-run=false interface=MemberRepository qualified-name=master-service/app/domain/repository version=v2.39.1
```

```Bash
randi@ottodigital:~/work/tdd-showcase/master-service/app/domain/repository 
$ go mod tidy
go: downloading github.com/stretchr/objx v0.1.0
go: finding module for package github.com/rogpeppe/go-internal/fmtsort
go: finding module for package github.com/kr/text
go: downloading github.com/rogpeppe/go-internal v1.12.0
go: found github.com/kr/text in github.com/kr/text v0.2.0
go: found github.com/rogpeppe/go-internal/fmtsort in github.com/rogpeppe/go-internal v1.12.0
```

These commands will generate mock file in new folder called mocks.

*master-service/app/domain/repository/mocks/member_repository_mock.go*
```Go
// Code generated by mockery v2.39.1. DO NOT EDIT.

package mocks

import (
	entity "master-service/app/domain/entity"

	mock "github.com/stretchr/testify/mock"
)

// MemberRepository is an autogenerated mock type for the MemberRepository type
type MemberRepository struct {
	mock.Mock
}

// Create provides a mock function with given fields: data
func (_m *MemberRepository) Create(data *entity.Member) (*entity.Member, error) {
	ret := _m.Called(data)

	if len(ret) == 0 {
		panic("no return value specified for Create")
	}

	var r0 *entity.Member
	var r1 error
	if rf, ok := ret.Get(0).(func(*entity.Member) (*entity.Member, error)); ok {
		return rf(data)
	}
	if rf, ok := ret.Get(0).(func(*entity.Member) *entity.Member); ok {
		r0 = rf(data)
	} else {
		if ret.Get(0) != nil {
			r0 = ret.Get(0).(*entity.Member)
		}
	}

	if rf, ok := ret.Get(1).(func(*entity.Member) error); ok {
		r1 = rf(data)
	} else {
		r1 = ret.Error(1)
	}

	return r0, r1
}

// NewMemberRepository creates a new instance of MemberRepository. It also registers a testing interface on the mock and a cleanup function to assert the mocks expectations.
// The first argument is typically a *testing.T value.
func NewMemberRepository(t interface {
	mock.TestingT
	Cleanup(func())
}) *MemberRepository {
	mock := &MemberRepository{}
	mock.Mock.Test(t)

	t.Cleanup(func() { mock.AssertExpectations(t) })

	return mock
}
```

Last thing in this step is, we add method RegisterMember to MemberUsecase.

*master-service/app/usecase/member_usecase.go*
```Go
package usecase

import (
	"master-service/app/domain/entity"
	"master-service/app/domain/model"
)

type memberUsecaseImpl struct {
}

// RegisterMember implements MemberUsecase.
func (*memberUsecaseImpl) RegisterMember(*model.MemberDto) (*entity.Member, error) {
	return nil, nil
}

type MemberUsecase interface {
	RegisterMember(*model.MemberDto) (*entity.Member, error)
}

func newMemberUsecaseImpl() MemberUsecase {
	return &memberUsecaseImpl{}
}
```

#### Step 5 - Setting up your test scenario
Going back to our member repository test code. First, we'll add suite package to use the suite functionalities from testify. Then, we add member repository interface from domain module.

*master-service/app/infrastructure/persistence/member_repository_impl_test.go*
```Go
package persistence

import (
	"master-service/app/domain/entity"
	"master-service/app/domain/repository"
	"master-service/pkg/util"
	"testing"

	"github.com/google/uuid"
	"github.com/jinzhu/configor"
	"github.com/stretchr/testify/suite"
)

type memberRepositorySuite struct {
	suite.Suite
	repository repository.MemberRepository
}
```
And then we create method SetupSuite for load config and connect to real database.
```Go
func (s *memberRepositorySuite) SetupSuite() {
	// start init config using yml
	err := configor.New(&configor.Config{ErrorOnUnmatchedKeys: true}).
		Load(&util.Config, "../../../config.development.yml")
	s.Require().NoError(err, "failed to load config")
	// connect db
	repos, err := NewRepositories()
	s.Require().NoError(err, "failed to connect database")

	s.repository = repos.Member
}
```
Then we add some test code to method TestCreateMember_Positive.
```Go
func (s *memberRepositorySuite) TestCreateMember_Positive() {
	data := entity.Member{
		MemberId:  uuid.New(),
		FullName:  "randi apriansyah",
		BirthDay:  "1997-01-01",
		Address:   "Cibinong City Bogor",
		CreatedBy: "system",
	}

	_, err := s.repository.Create(&data)
	s.Require().NoError(err, "error when create member")
}
```
At last line, we create method TestMemberRepository to call and execute all test method with command ```go test -v```
```Go
func TestMemberRepository(t *testing.T) {
	suite.Run(t, new(memberRepositorySuite))
}
```


After we done with fail member repository test code, let's move on to member usecase test code. We will add some property just like we do in member repository test code.

*master-service/app/usecase/member_usecase_test.go*
```Go
package usecase

import (
	"master-service/app/infrastructure/persistence"
	"master-service/pkg/util"

	"github.com/jinzhu/configor"
	"github.com/stretchr/testify/suite"
)

type memberUsecaseSuite struct {
	suite.Suite
	memberUsecase MemberUsecase
}
```
And then we create method SetupSuite same like member repository test.
```Go
func (u *memberUsecaseSuite) SetupSuite() {
	// start init config using yml
	err := configor.New(&configor.Config{ErrorOnUnmatchedKeys: true}).
		Load(&util.Config, "../../config.development.yml")
	u.Require().NoError(err, "failed to load config")
	// connect db
	repos, err := persistence.NewRepositories()
	u.Require().NoError(err, "failed to connect database")

	u.memberUsecase = NewMemberUsecaseImpl(repos.Member)
}
```
Then we add some test code to method TestRegisterMember_Positive.
```Go
func (u *memberUsecaseSuite) TestRegisterMember_Positive() {
	// arrange
	req := model.MemberDto{
		FullName: "randi",
		BirthDay: "1997-01-01", // valid date format
		Address:  "Bogor",
	}
	// act
	_, err := u.memberUsecase.RegisterMember(&req)
	// assert
	u.Require().NoError(err, "error when register member")
}
```
At last line, we create method TestMemberRepository to call and execute all test method with command ```go test -v```
```Go
func TestMemberUsecase(t *testing.T) {
	suite.Run(t, new(memberUsecaseSuite))
}
```

### Step 7 - Implement the functionality required by test scenario

*master-service/app/infrastructure/persistence/member_repository_impl.go*
```Go
// Create implements repository.MemberRepository.
func (r *memberRepositoryImpl) Create(data *entity.Member) (*entity.Member, error) {
	if err := r.db.Create(&data).Error; err != nil {
		return nil, err
	}
	return data, nil
}
```

*master-service/app/usecase/member_usecase.go*
```Go
// RegisterMember implements MemberUsecase.
func (u *memberUsecaseImpl) RegisterMember(req *model.MemberDto) (
	*entity.Member, error,
) {
	// transform dto to entity
	data := new(entity.Member)
	copier.Copy(&data, req)
	data.MemberId = uuid.New()
	return u.memberRepo.Create(data)
}
```
