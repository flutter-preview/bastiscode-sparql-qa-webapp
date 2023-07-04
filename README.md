# SPARQL question answering webapp

Web application for question answering backends.
Supports the following tasks:
- SPARQL generation

## Build

Configure your website using [lib/config.dart](lib/config.dart).
After that build it using `flutter build web --release`, or
if you do not have flutter installed use `docker build -t <image-name> .` which
creates a nginx docker image hosting the website. Run it using
`docker run -p <port>:80 <image-name>`.

## Endpoints

Endpoints required for all question answering backends:
- [GET] /models
```
  returns:
    {
      "task": "sparql generation", 
      "models": [
        {
          "name": "best_model_v1", 
          "description": "this model is so good", 
          "tags": ["arch::transformer", "speed::fast", "lang::en", ...]
        }, 
        ...
      ]
    }
```
- [GET] /info
```
  returns:
    {
      "gpu": ["GTX 1080Ti", "RTX 2080Ti", ...],
      "cpu": "Intel i7 9700K",
      "timeout": 10.0
    }
```

Also all endpoints should support arbitrary leading base URLs,
e.g. /api/v1/sparql/info

In particular, for this webapp, the tasks should be run with the
following base URLs:
- SPARQL generation: /api

Endpoints required for SPARQL generation:
- [POST] /answer
```
  requires:
    {
      "model": "best_model_v1", 
      "questions": "who is albert einstein?" | ["who is albert einstein?", "how old is angela merkel?"]
    }
  optional:
    {
      "search_strategy": "greedy",
      "beam_width": 5,
      "labels": false
    }
  returns:
    {
      "raw": "SELECT <bov>x<eov> WHERE ..." | ["SELECT <bov>x<eov> WHERE ...", "SELECT <bov>x<eov> WHERE ..."], 
      "runtime": {"s": 10, "b": 20}
    } 
  optional:
    {
      "sparql": "SELECT ?x WHERE ..." | ["SELECT ?x WHERE ...", "SELECT ?x WHERE ..."], 
    }
```