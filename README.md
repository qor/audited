# Audited

Audited is a [GORM](https://github.com/jinzhu/gorm) extension, used to save last UpdatedBy and CreatedBy for models

### Register GORM Callbacks

Audited is using [GORM](https://github.com/jinzhu/gorm)'s callbacks to handle audited, so you need to register callbacks to gorm DB first, do it like:

```go
import (
  "github.com/jinzhu/gorm"
  "github.com/qor/audited"
)

db, err := gorm.Open("sqlite3", "demo_db") // [gorm](https://github.com/jinzhu/gorm)

audited.RegisterCallbacks(db)
```

### Make Model Auditable

Embed `audited.AuditedModel` into your model as anonymous field, like:

```go
type Product struct {
	gorm.Model
	Name string
	audited.AuditedModel
}
```

### Usage

```go
var currentUser = User{ID: 100}
var product Product
var db, err = gorm.Open("sqlite3", "demo_db")
// ...

// Create will set product's CreatedBy, UpdatedBy to current user's primary key if it is a valid model
db.Set("audited:current_user", currentUser).Create(&product)
// product.CreatedBy => 100
// product.UpdatedBy => 100

// If not a valid model, then will set CreatedBy, UpdatedBy to current_user's value
db.Set("audited:current_user", "admin").Create(&product)
// product.CreatedBy => "admin"
// product.UpdatedBy => "admin"

// Saveing a record without primary key will also trigger `Create`, so CreatedBy, UpdatedBy will be updated
// When saving a record has primary key, then it will only update the `UpdatedBy`
db.Set("audited:current_user", "dev").Save(&product)
// product.UpdatedBy => "dev"
```

## [Qor Support](https://github.com/qor/qor)

[QOR](http://getqor.com) is architected from the ground up to accelerate development and deployment of Content Management Systems, E-commerce Systems, and Business Applications, and comprised of modules that abstract common features for such system.

Audited could be used alone, and it works nicely with QOR, if you have requirements to manage your application's data, be sure to check QOR out!

[QOR Demo:  http://demo.getqor.com/admin](http://demo.getqor.com/admin)

When use Audited with qor, if you has embedded `audited.AuditedModel` for any models, the model will be tracked when do creating/updating with Qor Admin

## License

Released under the [MIT License](http://opensource.org/licenses/MIT).
