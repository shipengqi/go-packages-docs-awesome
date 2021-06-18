Sometimes a test suite has some specific resource or state which has to be set up and then torn down for every test. This is a way to simplify that process by making it possible to wrap tests in helper functions.

This is useful for otherwise shared common state, like database transactions, where you set up the transaction, let the test operate on it, check that it is still alive and finally perform a rollback before the next test.

## Basic principle

The basic idea is this:

* Provide a function which performs the following:
  1. Accepts a closure `f` as a parameter which will be invoked with the initialized resource or state
  2. Creates and returns a `func()` which performs the following:
     1. Initializes the resource
     2. Provides a `Reset` for the resource, if needed
     3. Executes the closure `f`
* Then to use this function in tests:
  1. Create a closure which takes the initialized resource or state as a parameter
  2. Pass this closure to the created helper function
  3. Pass the result from the helper function as the block to a `Convey` invocation

**Note**: When reporting failures, the failure will be reported by traversing the stack trace looking for the first location in a file that ends with `_test.go`.  For this reason, it is important that calls to `So` appear in such a file.  Otherwise, the error will be reported at a different (probably higher) level in the stack and it will be unclear exactly what failure has occurred.

## Example

```go
package main

import (
	"database/sql"
	"testing"

	_ "github.com/lib/pq"
	. "github.com/smartystreets/goconvey/convey"
)

func WithTransaction(db *sql.DB, f func(tx *sql.Tx)) func() {
	return func() {
		tx, err := db.Begin()
		So(err, ShouldBeNil)

		Reset(func() {
			/* Verify that the transaction is alive by executing a command */
			_, err := tx.Exec("SELECT 1")
			So(err, ShouldBeNil)

			tx.Rollback()
		})

		/* Here we invoke the actual test-closure and provide the transaction */
		f(tx)
	}
}

func TestUsers(t *testing.T) {
	db, err := sql.Open("postgres", "postgres://localhost?sslmode=disable")
	if err != nil {
		panic(err)
	}

	Convey("Given a user in the database", t, WithTransaction(db, func(tx *sql.Tx) {
		_, err := tx.Exec(`INSERT INTO "Users" ("id", "name") VALUES (1, 'Test User')`)
		So(err, ShouldBeNil)

		Convey("Attempting to retrieve the user should return the user", func() {
			 var name string

			 data := tx.QueryRow(`SELECT "name" FROM "Users" WHERE "id" = 1`)
			 err = data.Scan(&name)

			 So(err, ShouldBeNil)
			 So(name, ShouldEqual, "Test User")
		})
	}))
}

/* Required table to run the test:
CREATE TABLE "public"."Users" ( 
	"id" INTEGER NOT NULL UNIQUE, 
	"name" CHARACTER VARYING( 2044 ) NOT NULL
);
*/
```

Output:

```
=== RUN TestUsers

  Given a user in the database ✔✔
    Attempting to retrieve the user should return the user ✔✔✔

--- PASS: TestUsers (0.01 seconds)
PASS
ok      test    0.022s
```