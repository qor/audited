# Audited

Audited is used to record the last User who created and/or updated your [GORM](https://github.com/jinzhu/gorm) model. It does so using a `CreatedBy` and `UpdatedBy` field

[![GoDoc](https://godoc.org/github.com/qor/audited?status.svg)](https://godoc.org/github.com/qor/audited)

### Register GORM Callbacks

Audited utilises [GORM](https://github.com/jinzhu/gorm) callbacks to log data, so you will need to register callbacks first:

```go
import (
  "github.com/jinzhu/gorm"
  "github.com/qor/audited"
)

db, err := gorm.Open("sqlite3", "demo_db")
audited.RegisterCallbacks(db)
```

### Make Model Auditable

Embed `audited.AuditedModel` into your model as an anonymous field to make the model auditable:

```go
type Product struct {
	gorm.Model
	Name string
	audited.AuditedModel
}
```

### Usage

```go
import "github.com/qor/audited"
import "github.com/jinzhu/gorm"

func main() {
  var db, err = gorm.Open("sqlite3", "demo_db")
  var currentUser = User{ID: 100}
  var product Product

  // Create will set product's `CreatedBy`, `UpdatedBy` to `currentUser`'s primary key if `audited:current_user` is a valid model
  db.Set("audited:current_user", currentUser).Create(&product)
  // product.CreatedBy => 100
  // product.UpdatedBy => 100

  // If it is not a valid model, then will set `CreatedBy`, `UpdatedBy` to the passed value
  db.Set("audited:current_user", "admin").Create(&product)
  // product.CreatedBy => "admin"
  // product.UpdatedBy => "admin"

  // When updating a record, it will update the `UpdatedBy` to `audited:current_user`'s value
  db.Set("audited:current_user", "dev").Model(&product).Update("Code", "L1212")
  // product.UpdatedBy => "dev"
}
```

## [Qor Support](https://github.com/qor/qor)

[QOR](http://getqor.com) is architected from the ground up to accelerate development and deployment of Content Management Systems, E-commerce Systems, and Business Applications and as such is comprised of modules that abstract common features for such systems.

Audited could be used alone, but it works very nicely with QOR - if you have requirements to manage your application's data, be sure to check QOR out!

[QOR Demo:  http://demo.getqor.com/admin](http://demo.getqor.com/admin)

To use Audited with QOR, just embedded `audited.AuditedModel` for a model, it will subsequently be tracked whenever creating/updating in Qor Admin.

## License

Released under the [MIT License](http://opensource.org/licenses/MIT).
