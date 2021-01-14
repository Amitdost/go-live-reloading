Using Live Reload in Go


When we're developing APIs in Go or any other language it's important to have tools that help us to automate certain tasks like Live Reload, I'd had been using realize to doing it, but it seems to have some issues when working with Go modules, I tried some different solutions and they didn't work for me 😭.
Luckily I found a very handy package that can helps us to achieve this task adn it's called modd, let's give it a try and see how it works:

Create the basic project structure
`$ mkdir live-reload && cd live-reload && mkdir cmd
$ go mod init
$ touch cmd/main.go`
Open main.go file and type the following
package main

import (
    "fmt"
    "log"
    "net/http"
)

func main() {

    mux := http.NewServeMux()

    mux.HandleFunc("/", homeHandler)
    mux.HandleFunc("/contact", contactHandler)

    log.Println(fmt.Sprintf("Server running on http://localhost%s 🐹", ":4000"))
    err := http.ListenAndServe(":4000", mux)
    if err != nil {
        log.Fatalf("could not run the server %v", err)
        return
    }
}

func homeHandler(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Hello from home handler"))
}

func contactHandler(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Hello from contact handler"))
}

Installing modd package
$ brew install modd
Create modd config file
# run _only_ changed tests
**/*.go {
    prep: go test @dirmods
}

# run and rebuild the app
# make sure you type your app name adter -o flag and daemon command
**/*.go !**/*_test.go {
    prep: go build -o live-reload ./cmd
    daemon +sigterm: ./live-reload
}

Then execute modd command inside root project:
$ modd
You should see something like this in your terminal:

modd running

Go to your main.go file and update homeHandler
//... other code
func homeHandler(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("Hello from home handler changed and live reload 😎"))
}
//... rest of the code
After that modd is going to re-compile your server and now you can visit http://localhost:4000 and see the updated version, your terminal should look like so:

Conclusion
Hope it helps, happy coding 👨🏽‍💻
