aWOT
==========

Arduino web server library.

## Documentation
[awot.net](https://awot.net)

## Hello World Example
```cpp
#include <WiFi.h>
#include <aWOT.h>

WiFiServer server(80);
Application app;

void index(Request &req, Response &res) {
  res.print("Hello World!");
}

void setup() {
  Serial.begin(115200);

  WiFi.begin("ssid", "password");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println(WiFi.localIP());

  app.get("/", &index);
  server.begin();
}

void loop() {  
  WiFiClient client = server.available();

  if (client.connected()) {
    app.process(&client);
  }
}
```

### Query parameters
```cpp
// HTTP GET /cats?type=lolcat
void queryParams(Request &req, Response &res) {
  char type[64];
  req.query("type", type, 64);

  res.print(type); // "lolcat"
}

void setup() {
  // other setup ...
  app.get("/cats", &queryParams);
}
```

### Route paramaters
```cpp
// HTTP GET /cats/lolcat
void routeParams(Request &req, Response &res) {
  char catId[64];
  req.route("catId", catId, 64);

  res.print(catId);
}

void setup() {
  // other setup
  app.get("/cats/:catId", &routeParams);
}
```

### Post parameters
```cpp
void postParams(Request &req, Response &res) {
  char name[10];
  char value[64];

  while (req.contentLeft()) {
    req.postParam(name, 10, value, 64);
    res.print(name);
    res.print(": ");
    res.println(value);
  }
}

void setup() {
  // other setup

  app.post("/form", &postParams);
}
```

### Reading and writing headers
```cpp
char userAgentBuffer[200];

// HTTP GET /headers
void headers(Request &req, Response &res) {
  char * userAgent = req.get("User-Agent"); // "Mozilla/5.0 (Macintosh; Inte ...."

  res.set("Cookie", "lolcat"); // will set Cookie header value to "lolcat"
  res.print(userAgent);
}

void setup() {
  // other setup

  // header names are handled case insensitive
  app.header("User-Agent", userAgentBuffer, 200); 
  app.get("/useragent", &headers);
}
```

### Routers
```cpp
Application app;
Router cats("/cats");

void looooong(Request &req, Response &res) {
  res.success("text/html");
  res.print("looooong cat is long!");
}

void ceiling(Request &req, Response &res) {
  res.success("text/html");
  res.print("ceiling cat is watching you debug!");
}

void nyannyan(Request &req, Response &res) {
  res.success("text/html");

  for (int i = 0; i < 100; i++) {
      res.print("nyan ");
  }
}

void setup() {
  // other setup

  cats.get("/long", &looooong);
  cats.get("/ceiling", &ceiling);
  cats.get("/nyan", &nyannyan);

  app.route(&cats);
}
```

## Acknowledgements
Based on Webduino, Copyright 2009-2014 Ben Combee, Ran Talbott, Christopher Lee, Martin Lormes, Francisco M Cuenca-Acuna

## Licence
MIT
