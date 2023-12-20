# Entities and Repositories

First thing first, we will create member, staff, book, transaction and detail transaction entity based on class diagram.


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


*master-service/domain/entity/staff.go*
```Go
package entity

import (
	"time"

	"github.com/google/uuid"
)

type Staff struct {
	StaffId   uuid.UUID  `json:"staff_id"`
	FullName  string     `json:"full_name"`
	BirthDay  string     `json:"birth_day"`
	CreatedAt time.Time  `json:"created_at"`
	CreatedBy string     `json:"created_by"`
	UpdatedAt *time.Time `json:"updated_at"`
	UpdatedBy string     `json:"updated_by"`
}
```


*master-service/domain/entity/book.go*
```Go
package entity

import (
	"time"

	"github.com/google/uuid"
)

type Book struct {
	BookId      uuid.UUID  `json:"book_id"`
	Isbn        string     `json:"isbn"`
	Category    string     `json:"category"`
	Title       string     `json:"title"`
	Author      string     `json:"author"`
	Publisher   string     `json:"publisher"`
	Edition     string     `json:"edition"`
	TotalPages  int64      `json:"total_pages"`
	DatePublish string     `json:"date_publish"`
	CreatedAt   time.Time  `json:"created_at"`
	CreatedBy   string     `json:"created_by"`
	UpdatedAt   *time.Time `json:"updated_at"`
	UpdatedBy   string     `json:"updated_by"`
}

```

*transaction-service/domain/entity/transaction.go*
```Go
package entity

import (
	"time"

	"github.com/google/uuid"
)

type Transaction struct {
	TransactionId uuid.UUID  `json:"transaction_id"`
	MemberId      uuid.UUID  `json:"member_id"`
	StaffId       uuid.UUID  `json:"staff_id"`
	BorrowAt      time.Time  `json:"borrow_at"`
	TotalLateDays int64      `json:"total_late_days"`
	CreatedAt     time.Time  `json:"created_at"`
	CreatedBy     string     `json:"created_by"`
	UpdatedAt     *time.Time `json:"updated_at"`
	UpdatedBy     string     `json:"updated_by"`
}

```


*transaction-service/domain/entity/detail_transaction.go*
```Go
package entity

import (
	"time"

	"github.com/google/uuid"
)

type DetailTransaction struct {
	DetailTransactionId uuid.UUID  `json:"detail_transaction_id"`
	TransactionId       uuid.UUID  `json:"transaction_id"`
	BookId              uuid.UUID  `json:"book_id"`
	DueDate             time.Time  `json:"due_date"`
	ReturnDate          time.Time  `json:"return_date"`
	CreatedAt           time.Time  `json:"created_at"`
	CreatedBy           string     `json:"created_by"`
	UpdatedAt           *time.Time `json:"updated_at"`
	UpdatedBy           string     `json:"updated_by"`
}

```


And then, we