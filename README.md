# <img src="https://gamepedia.cursecdn.com/dota2_gamepedia/f/f5/Main_Page_icon_Heroes.png" width="40" height="40" alt=":morphling:" class="emoji" title=":morphling:"/> Morphling [![Build Status](https://travis-ci.org/cikupin/morphling.svg?branch=master)](https://travis-ci.org/cikupin/morphling) [![GoDoc](https://godoc.org/github.com/cikupin/morphling?status.svg)](https://godoc.org/github.com/cikupin/morphling)

Morphling is a SQL wrapper for database replication topology. Morphling helps you to determine which connection to be
used when execute query to the sql database. **It's not an ORM**

_**Note** : This a modified version version of [morphling](https://github.com/ahartanto/morphling) library with an additional feature. Please refer to the original repo for the original features._

**Why you need morphling ?**
Golang sql package is allowed you to open multiple connection, but without any restriction, means any connection you
opened, can do all SQL manipulation (`select / read`, `insert`, `update`, `delete`, `select`), While slave database supposed to be only allowed to do `select` , with morphling it manage the master slave connection usage.

**Why morphling can only specify one replica ?** We can use load balancer such as HAProxy or else to handle a replica
set, its provide abstraction to how you want to handle your replica sets load, either round robin or else.

#### Example

```go
package main

import (
    "fmt"
    "github.com/cikupin/morphling"
    _ "github.com/go-sql-driver/mysql"
)

func main() {

    var urlFormat = "%s:%s@tcp(%s:%d)/%s?charset=%s&parseTime=true&loc=Local"

    // Prepare data source for master connection
    masterUser := "user"
    masterPassword := "password"
    masterHost := "masterhost"
    masterPort := "3306"
    masterDBName := "dbname"
    masterCharset := "utf8"
    dataSourceMaster := fmt.Sprintf(urlFormat, masterUser, masterPassword,masterHost, masterPort, masterDBName, masterCharset)

    // Prepare data source for slave connection
    slaveUser := "user"
    slavePassword := "password"
    slaveHost := "slavehost"
    slavePort := "3306"
    slaveDBName := "dbname"
    slaveCharset := "utf8"
    dataSourceSlave := fmt.Sprintf(urlFormat, slaveUser, slavePassword,slaveHost, slavePort, slaveDBName, slaveCharset)

    // Get database handler
    db, err := morphling.OpenConnection(morphling.MySQLDriver, dataSourceMaster, dataSourceSlave)
    if err != nil {
        panic(fmt.Errorf("failed dial database : %v", err))
    }

    // Then you can do the query as golang sql package does
    var id int
    q := fmt.Sprintf("SELECT id FROM accounts LIMIT 1")
    err = db.QueryRow(q).Scan(&id)
    fmt.Printf(id)

}
```

# About

Fun fact, *Morphling* is a ranged agility **dota** hero that has many flexible abilities. His ultimate, Morph,
transforms him into a copy of an enemy using their stats and basic abilities.

Morphling can be as an analogy of database replication, `select / read` data is the only basic abilites that replica has,
while `select / read`, `insert`, `update` and `delete` is the main abilites.
