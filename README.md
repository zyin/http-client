# http-client

This repository calls HTTP APIs per the configuration

# Usage

http-client -n \<concurrent connection number\> -f \<api data file\>,\<api data file\>

The default concurrent connection number is 1

The API data file must be the full path.

# mechanism

The http-client tool is using the generator and worker model to call the APIs.

The tool creates a generator for each API data file to generate the data.

The generator places the data into the queue and the worker will call the API after retrieving data from the queue.

# Format

The data file is defined in json format.
```json
{
    "run": "concurrent or order"
    "server": "https://www.example.com",
    {
        "name": "example 1",
        "order": 1,
        "method": "PUT, GET, POST, PATCH or DELETE",
        "count": 10,
        "endpoint": "/api/v1/...",
        "header": {
            "application": "application/json",
            "authentication": "bearer $environment.variable",
            ...
        },
        "data": {
            "field name": "value 1",
            ...
        }
    }
    {
        "name": "example 2",
        "order": 2,
        "method": "PUT, GET, POST, PATCH or DELETE",
        "count": 40,
        "endpoint": "/api/v1/...",
        "header": {
            "application": "application/json",
            "authentication": "bearer $environment(variable)",
            ...
        },
        "data": {
            "field name1": "$uuid(32)-fixvalue-othervalue",
            ...
        }
    }
}
```

The "run" value determines the type of calling APIs
* concurrent - the generator will generate data for each API in round-robin algorithm.
* order - the generator will generate data for N-th API before generating data for the (N+1)-th order API. The N starts from 1.

# Built-in functions

The tool has 3 built-in functions as below, more functions will be provided.

* environment(variable-name) - get the value of the environment variable. If it does not exist, the tool will exist with error message.
* uuid(length) - get the substring of the uuid with specified length.
* randome(begin, end) - get the random integer number between begin and end.

The tool will exit after failing to validate the functions with parameters. For example, the environment variable doesn't exist. Or the length of uuid is too big.