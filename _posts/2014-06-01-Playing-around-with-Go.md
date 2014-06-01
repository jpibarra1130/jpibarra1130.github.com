---
layout: post
title: "Playing Around with Go"
---

I've been interested recently with using Go thanks to a few friends of mine who have been raving about how good it was. I finally decided to explore it a few weeks ago. I started off with trying out the [Golang tour](http://tour.golang.org). After that, I've proceeded reading around the documentation. I must say that the documentation for Go is quite comprehensive.

Last week, I was discussing a possible project I wanted to start with my friend. I thought it would be interesting to use Go to create the API for this. Prior to starting this project, we've decided to play around with the languages we plan to use. I was assigned the backend component and decided to use Golang for it. I wanted to create a simple API so that I will be able to explore the following areas:

1. Create an HTTP endpoint
2. Create DB migrations
3. Map the db to structs
4. Handle dependency management
5. Test (which I am still figuring out as of writing)

It proved out to be quite simpler than I imagined.

Create an HTTP endpoint
-------------

The first thing I did was to figure out a way to make it run, and see something printed out on my browser which is the first goal.

To create the HTTP endpoint, I've decided to use [gorilla-mux](https://github.com/gorilla/mux). You need to go to $GOPATH/src/github.com/user/simple-api-go. Create a file named ```simple_api.go```. Make sure to install gorilla-mux first before you run this.


    import (
      "encoding/json"
      "github.com/gorilla/mux"
      "log"
      "net/http"
    )

    func main() {
      r := mux.NewRouter()
      r.HandleFunc("/", PostsHandler).Methods("GET")

      http.Handle("/", r)

      log.Println("Server started. Listening...")
      http.ListenAndServe(":3000", nil)
    }

    func PostsHandler(w http.ResponseWriter, r *http.Request) {
      w.Write([]byte("hello world"))
    }


Once you save this, run ```go build```. This will generate an executable file ```simple_api_go``` in the main folder. Simply run this, and go to ```localhost:3000```. You should be able to see 'hello world'.

Create DB migrations
-------------

The next step is to see how I'll be able to manage DB migrations in this project. I was googling around a possible library to use. I ended up using [Goose](https://bitbucket.org/liamstask/goose). Once you have this installed, create a database for simple-api-go. Create ```db/dbconf.yml``` in your simple-api-go and fill it in with the necessary information. I am currently using MySQL. Myd dbconf.yml looks something like this:

    development:
      driver: mymysql
        open: tcp:localhost:3306*simple-api-go-development/user/password

To create a simple migration, you need to run this

    goose create CreatePosts

This will generate a migration file inside ```db/migrations/```. Edit the generated migration, and add this simple script.

    -- +goose Up
    CREATE TABLE posts (
        id int NOT NULL,
        title text,
        body text,
        PRIMARY KEY(id)
    );

    -- +goose Down
    DROP TABLE posts;

After that, run ```goose up```. This will run the script above. The database you have created should now have the the ```posts``` table. Read more about goose [here](https://bitbucket.org/liamstask/goose).

Map the db to structs
-------------
Now, I needed to find out a way to easily access the info from the database. To do this, I've decided to use [gorp](https://github.com/coopernurse/gorp). First thing you need to do is update simple_api.go and create a struct Post with the gorp annotations.

    type Post struct {
      Id    int    `db:"id"`
      Title string `db:"title"`
      Body  string `db:"body"`
    }

Once you have the struct setup, you need to create a new function which will connect to the database to retrieve the information:

    // global options. available to any subcommands. This was taken from goose library
    var flagPath = flag.String("path", "db", "folder containing db info")
    var flagEnv = flag.String("env", "development", "which DB environment to use")

    func GetPosts() []Post {

      // I've decided to use the goose config file so that I won't have multiple db configs
      conf, err := goose.NewDBConf(*flagPath, *flagEnv)

      if err != nil {
        log.Fatal(err)
        return nil
      }

      log.Printf("Connecting to %v, %v", conf.Driver.Name, conf.Driver.OpenStr)

      db, err := sql.Open(conf.Driver.Name, conf.Driver.OpenStr)

      dbmap := &gorp.DbMap{Db: db, Dialect: gorp.MySQLDialect{"InnoDB", "UTF8"}}
      defer dbmap.Db.Close()

      var posts []Post
      _, err = dbmap.Select(&posts, "select id, title, body from posts order by id")

      if err != nil {
        log.Printf("Error: ", err)
        return nil
      }

      return posts
    }

You can read more about the gorp related functionalities [gorp](https://github.com/coopernurse/gorp). Once you have this up and running, you just need to update your PostsHandler to something like this to incorporate the function we just created which gets the data from the db.

    func PostsHandler(w http.ResponseWriter, r *http.Request) {
      out, err := json.Marshal(GetPosts())

      if err != nil {
        log.Fatal(err)
        w.Write([]byte("Something bad has happened."))
        return
      }

      log.Printf("Post: %v", string(out))

      w.Write([]byte(out))
    }

Now, do a ```go build``` and run the generated file. If you go to ```localhost:3000``` you should be able to see the posts json response. Please make sure to run all the necessary imports.

If you want to see the final simple-api-go for your reference, you can clone or fork [simple-api-go](https://github.com/jpibarra1130/simple-api-go).