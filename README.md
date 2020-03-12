# Getting started Tutorial - Restful APIs with Golang

## Disclaimer
I did this tutorial when I barely knew the language and I was still a junior developer. Some information that I can give in this tutorial may no longer be up-to-date.

------------------

After couple years of developing web applications, I wanted something new, something different. I was looking for new technologies which have good support, good community and good perspectives. You will say that it may sounds obvious, nobody will turn to Basic Programming right now and you are right. But I needed a nice introduction for my tutorial.

So, in fact, few  weeks ago, I didn't even know anything about Go Programming, just strict basics like who's behind and what's it made for but nothing more.
I want to share with you, the way I learned Golang, that allow me today to start developing RestFul APIs as I wanted.
To do so, what better way than creating bar/brewery!
It may sound crazy but it's not, we will together understand the concepts behind this by serving beers.

![beer taps](https://image.ibb.co/jysRcc/beer_tap_2435408_1920.jpg)

## Introducing Golang
Before any beers, we need to introduce what is Golang.
Its development began at Google in 2007 and was released in 2009.
It was thought to be a robust and fast language. It combines the benefits of few other technologies such as C, C++ and Python while avoiding some features of modern languages (methods, type inheritance).
It was also design for clarity with user-friendly syntax.
It is one of the best for quick compilation, concurrency developments and parallel executions.
I did not made it up, this is one of the creators of the language, Rob Pike, that said: "You can compile and run a go program faster than some interpreters can even start".
More recently, it can also be used to build mobile apps on Android and iOS devices.

> This tutorial is not about learning Go syntax, if you want to do so, I truly recommend you to visit the websites that I suggest at the end of this tutorial.

Its favorite playgrounds :
* Multicore performance
* Concurrency
* Cloud Computing
* Microservices / API

This is exactly what we are looking for. We need an efficient way to build our brewery and serve beers quickly to our customers.

## Building

 ### Prerequisites
What is more obvious than installing Go on your machine? For this, nothing more easy, go to: https://golang.org/dl/ and follow the instructions depending of your OS.
*Personally, I use a Linux distribution and I use [Goland](https://www.jetbrains.com/go/) from JetBrains as IDE, but you can also use [Visual Source Code](https://code.visualstudio.com/) which have good support and plugins for Go.*

We will also need something to call your routes, something like [Postman](https://www.getpostman.com/).


### Goals
As I said before we are going to build a bar/brewery. Here come some explanations.
There will be 2 services, one for the bar, and another for the brewery. The customers will ask for beers to the bar and it will give beers to the customers.
If the bar is out of stock, it will ask for barrels of beer to the brewery.
If the brewery is out of barrels, it will start producing new barrels.

![architecture](https://image.ibb.co/kUqJqx/brewery_2.png)

> Here, stocks can be related to databases. For the sake of simplicity, this tutorial won't aboard part about working with databases, but I've linked some useful information at the end of it.

*The complete project is available on GitHub. The link is at the end.*

### Initializing
Our bar's name is going to be **The green dragon**, reference to *The Lord of the Rings*, so let's create our project folder into the ```$GOPATH``` directory.

> If you want more information about this, I invite you to visit: [How to write go code](https://golang.org/doc/code.html) by Golang Team and this article about [go project layout](https://medium.com/golang-learn/go-project-layout-e5213cdcfaa2)

```bash
$ mkdir theGreenDragon
```
> Note that structuring files in a go project is a bit confusing. For a while, I wanted something like I always known, well divided packages and not too many files. But I understood that in Go, it does not really matter. In fact, organizing code like it would be done in Java, Python, or NodeJS is a bad thing, you're probably fighting with the language if you do so. [This article](https://medium.com/@benbjohnson/structuring-applications-in-go-3b04be4ff091) has helped me feeling better about my project architecture.

We will use a third-party library for routing called [Mux](https://github.com/gorilla/mux). To install it, *if it's not already done*, just execute the command below:

```bash
$ go get -u github.com/gorilla/mux
```

Any Go program start with a func called ```main``` in the package ```main```, so let's create ```main.go``` at the root of our workspace, and fill it with some basic instructions:

```go
package main

import (
    "fmt"
    "github.com/gorilla/mux"
    "log"
    "net/http"
)


func main(){
    fmt.Println("Welcome to the The Green Dragon !")
    router := mux.NewRouter()
    log.Fatal(http.ListenAndServe(":8000", router))
}
```

Once you've done this, you have 2 possibilities:
* Run ``` $ go run main.go```
* Run ``` $ go build ```. It's going to compile and create an executable. Then, run ```$ ./theGreenDragon ```

> Remember these commands, you will need them each time you want to execute your program.

You've now opened your bar, or at least a server on port ```8000```. Next step, we will add routes and handlers.

### Routes and Handlers

We are going to create separates routes for the bar and for the brewery:
* ```/bar``` (GET) : All the informations about the bar and the beers they're serving
* ```/bar/{id}``` (GET) : Additional informations about a beer
* ```/bar``` (POST) : Order a beer
* ```/bar``` (DELETE) : When you're too drunk, it can happens that you break your mug of beer.
* ```/brewery``` (POST) : Order a barrel of beer
* ```/brewery``` (PUT) : Produce new barrels
> Some of these routes should be restricted with authentication, but this is not the purpose of the tutorial, we will try to keep things simple.

To add a route/handler, it's quite easy:
```go
router.HandleFunc("/pathToTheAction", MyFunction).Methods("A_HTTP_METHOD")
```

Let's see, what it looks like with our project:
```go
func main(){
    fmt.Println("Welcome to the The Green Dragon !")
    router := mux.NewRouter()

    buildBarRoutes(router)
    buildBreweryRoutes(router)

    log.Fatal(http.ListenAndServe(":8000", router))
}

//Bar's routes
func buildBarRoutes(router *mux.Router) {
    prefix := "/bar"
    router.HandleFunc(prefix, GetInfo).Methods("GET")
    router.HandleFunc(prefix + "/{id}", GetBeerInfo).Methods("GET")
    router.HandleFunc(prefix, OrderBeer).Methods("POST")
    router.HandleFunc(prefix, BreakMug).Methods("DELETE")
}

//Brewery's routes
func buildBreweryRoutes(router *mux.Router) {
    prefix := "/brewery"
    router.HandleFunc(prefix, OrderBarrels).Methods("POST")
    router.HandleFunc(prefix, ProduceBarrels).Methods("PUT")
}
```

You should see red right now :D. That's because we have to implement those functions. For that, I decided to create to more file ```barHandlers.go``` and ```breweryHandlers.go ```. Let’s add to them the handlers of our routes.

```go
//barHandlers.go
package main

import "net/http"

func GetInfo(writer http.ResponseWriter, request *http.Request) { }

func GetBeerInfo(writer http.ResponseWriter, request *http.Request) { }

func OrderBeer(writer http.ResponseWriter, request *http.Request) { }

func BreakMug(writer http.ResponseWriter, request *http.Request) { }


//breweryHandlers.go
package main

import "net/http"

func OrderBarrels(writer http.ResponseWriter, request *http.Request) { }

func ProduceBarrels(writer http.ResponseWriter, request *http.Request) { }
```

Let's explain quickly how are composed these functions. There are 2 parameters, their names are quite evoking, the ```writer``` allows managing what is related to the response and the ```request``` contains everything you have to know about... request.

> Since we add new files and if you use the bash command ```go run main.go``` you may want to add the newer files following this command. For example ```go run main.go barHandlers.go breweryHandlers.go```.
> If you don't want to, you can always execute the ``` go build``` command.

You should now be able to run your program and access these routes.

### Data
We talked about beers, barrels and mugs but we don't know how to represent those in our application. It's time to see how to create types!
For this, I decided to create a file ```database.go``` which will be our database. So, it will contain the models and the lists we need.

```go
package main

import "time"

var beers []Beer
var barrels []Barrel

type Beer struct {
    ID int   `json:"id"`
    Name string   `json:"name,omitempty"`
    Price  float32   `json:"price,omitempty"`
    PercentProof float32 `json:"percentProof,omitempty"`
    IPA bool `json:"ipa"`
}

type Barrel struct {
    Beer *Beer `json:"beer"`
    Quantity int `json:"quantity"`
    DateOfManufacture time.Time `json:"dateOfManufacture"`
}

type Mug struct {
    Beer *Beer `json:"beer"`
    Quantity int `json:"quantity"`
}

// Initializing datas
func initDatas() {
    //Beers
    gandalf := Beer{ID: 0, Name: "Gandalf", Price: 5, PercentProof:8, IPA: false}
    aragorn := Beer{ID: 1, Name: "Aragorn", Price: 5.5, PercentProof:7.5, IPA: true}
    sauron := Beer{ID: 2, Name: "Sauron", Price: 7, PercentProof: 11, IPA: false}

    beers = append(beers, gandalf, aragorn, sauron)

    //Barrels
    barrels = append(barrels,
        Barrel{&gandalf, 1000, time.Now()},
        Barrel{&aragorn, 8000, time.Now()},
        Barrel{&sauron, 0, time.Now()})
}
```
The types' attributes are annoted with json properties, this is how you can create a link between your types and json objects.
As we can see, ```Barrel```  and ```Mug``` contains a reference to the ```Beer``` they contain.
I also created a method in order to initialize the lists. I call this method in my ```main()``` function, right before I build the routes.

### Coding !
Let's add some code into our handlers in order to do something when we ask for beers ! To begin, we will complete the ```GET``` methods for the bar.

```go
import (
    "net/http"
    "encoding/json"
    "log"
    "github.com/gorilla/mux"
    "strconv"
)

func GetInfo(writer http.ResponseWriter, request *http.Request) {
    log.Println("Get infos about beers")
    initHeaders(writer)
    json.NewEncoder(writer).Encode(beers)
}

func GetBeerInfo(writer http.ResponseWriter, request *http.Request) {
    initHeaders(writer)
    //Converts the id parameter from a string to an int
    id, err := strconv.Atoi(mux.Vars(request)["id"])
    if err == nil {
        log.Println("Get info about beer id #", id)

        //Retrieves the infos about the beer
        beer := FindBeerByID(id)
        json.NewEncoder(writer).Encode(beer)
    } else {
        log.Fatal(err.Error())
    }
}

func initHeaders(writer http.ResponseWriter) {
writer.Header().Set("Content-Type", "application/json")
}

```

It's quite easy for now, we iterate over the beers and when it matches with the query, we use the library ```json``` to encode the response.
You can see a function ```FindBeerByID``` in ```database.go``` This is used in order to simulate a query to my database. We definitely should test if the return of the function is not empty but let's say it always return what we asked for.
Now that we know what to order, we will implement the feature!

```go
func OrderBeer(writer http.ResponseWriter, request *http.Request) {
    log.Println("Order a beer")
    initHeaders(writer)
    var order Order

    //Decodes the request and put the content of the body into the order
    _ = json.NewDecoder(request.Body).Decode(&order)

    //Retrieves the infos about the beer he wants to order
    beer := FindBeerByID(order.ID)

    numberOfBeerWanted := order.Quantity / mugQuantity
    //If the customer sent enough money
    if order.Money >= beer.Price * float32(numberOfBeerWanted) {
        mugs := serveBeer(&order, numberOfBeerWanted)

        json.NewEncoder(writer).Encode(mugs)
    } else {
        json.NewEncoder(writer).Encode("No enough money")
    }
}
```
I've created a little structure which represents the order. It contains the ID of the beer the customer wants to drink, the quantity and the money to pay the bar.

```go
type Order struct {
    ID int `json:"id"`
    Money float32 `json:"money"`
    Quantity int `json:"quantity"`
}
```

As you can see, I've also created one function ```serveBeer``` in ```barHandlers.go```. It checks if there is enough beer, if not, it will ask to brewery a new barrel by calling the path ```/brewery``` via POST method. Then it serves the beers and returns the mugs.

> To keep this tutorial short, I did not include those codes inside, please visit or clone the [GitHub project](https://github.com/Riverside14/golang-restful-api-tutorial)  associated with the tutorial.
> You may have noticed that some of the functions' name begins with a capital letter and some other is not. You can visit this [StackOverflow thread](https://stackoverflow.com/questions/38616687/which-way-to-name-a-function-in-go-camelcase-or-semi-camelcase) or the [official documentation](https://golang.org/doc/effective_go.html#names).

I skip the part about breaking a mug, there is nothing very interesting to see here. You can implements whatever you like. Personally, I added a counter that counts every mug that broke. But we could imagine a limited number of mugs where people have to give back their mugs after finishing their beers or something like that. This could be a great exercise for concurrency ;)

### The brewery
In the previous chapter, we saw that the ```ServeBeer``` function ask for a new barrel if the bar is out of stock. Here's the code of the brewery:
```go
func OrderBarrels(writer http.ResponseWriter, request *http.Request) {
    log.Println("Order new Barrel")
    initHeaders(writer)

    var requestedBarrel Barrel
    _ = json.NewDecoder(request.Body).Decode(&requestedBarrel)

    //It tries to find the barrel in stock.
    barrel, idx := FindBarrelFromBreweryByBeer(requestedBarrel.Beer)

    //If idx is inferior than 0, we need to produce new barrels
    if idx < 0 {
        //Initializes a client
        client := &http.Client{}
        //Prepares a PUT Request to http://localhost:8000/brewery with no body
        request, _ := http.NewRequest(http.MethodPut, "http://localhost:8000/brewery", nil)
        //Sends the request
        client.Do(request)
        //"Reloads" the barrel
        barrel, idx = FindBarrelFromBreweryByBeer(requestedBarrel.Beer)
    }

    requestedBarrel = barrel
    //Removes the barrel from the stock
    breweryBarrels = append(breweryBarrels[:idx], breweryBarrels[idx+1:]...)

    json.NewEncoder(writer).Encode(requestedBarrel)
}

func ProduceBarrels(writer http.ResponseWriter, request *http.Request) {
    log.Println("Producing new Barrels")

    breweryBarrels = append(breweryBarrels,
        Barrel{&beers[0], 1000, time.Now()},
        Barrel{&beers[1], 5000, time.Now()},
        Barrel{&beers[2], 3000, time.Now()})
}
```
We could totally call directly the function ```ProduceBarrels()``` from ```OrderBarrels``` but I wanted to show how to create a HTTP request. It could be useful when it comes to accest another service.
The ```ProduceBarrels``` function is quite easy. Each time we call it, we create one barrel of each type of beer. Despite everything, we should think about checking that after producing the barrels, we have enough beer to satisfied the initial order, otherwise, we should produce new barrels until we fulfill the need. But once again, keep this tutorial simple.

### Final Chapter
You can now launch the program and try to get infos about beers, order one and produce barrels of beer by calling the API.
In this tutorial, I chose to not explain how create simple CRUD operations over REST method using Golang. You will find easily one on the Internet. Personally, I wanted something a bit fun, with a different approach. I especially wanted to talk about being called and calling other services.
Note that all the code is available right [here](https://github.com/Riverside14/golang-restful-api-tutorial).
> Be aware that I add several comments in the final project, if some of those lines above was not clear, you probably want to check it.

### What's missing ?
There is a lot of things that we could do to improve this getting started tutorial. We could write book about this !
If you want to go further, here some ideas:
* Authentication
* Validation
* Unmock the database
* Testing
* Refactoring

Thanks for reading, I hope you've learned something. If you liked the tutorial, please share it and give me your feedbacks !

## Useful Links
### Code & Files
* [GitHub - Golang Restul API Tutorial](https://github.com/Riverside14/golang-restful-api-tutorial)

### Getting started with Go syntax
* [Go By examples](https://gobyexample.com/)
* [Go Programming - Derek Banas](https://www.youtube.com/watch?v=CF9S4QZuV30)

### Databases
* [Wiki SQLDrivers & Links to libraries](https://github.com/golang/go/wiki/SQLDrivers)
* [Wiki SQL Interfaces](https://github.com/golang/go/wiki/SQLInterface)

### Unit Tests
* [justforfunc #16: unit testing HTTP servers](https://www.youtube.com/watch?v=hVFEV-ieeew)
* [Testing in Golang - Thejas Babu](https://medium.com/@thejasbabu/testing-in-golang-c378b351002d)
