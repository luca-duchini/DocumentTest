OE - Example of Check Dependency
--------------------------------

## Check pizzatype vs toppings

All the following examples contain some placeholder that needs to be replaced. For example :

    1. {name} => TypeAndToppings
    2. {version} => 1.0.0
    3. {url} => http://rd-mc-dev3-cms01v.lab.nuance.com:8277/VARS/PizzaDemo/oe-var-dependency-checking-type-toppings.zip

###1.a CE-request

NCE send a agentCollected event to OE for pizzatoppings:

```json
{
    "sessionContext": {
        "privateState": {
           "outcomeEngine": {}
        },
        "dialogState":{
         "agentsWithValue":{
                  "pizzatoppings": {
                      "value": "sausage"
                  },
                  "pizzatype": {
                      "status": "CONFIRMED",
                      "value": "vegetarian"
                  }
         }
        }
    },
   "model": {
     "name": "{name}",
     "version": "{version}",
     "url": "{url}"
    },
    "events": [
        {
            "name": "AgentCollected",
            "params": {
               "uid": "pizzatoppings"
            }
        }
    ]
}
```

###1.b OE-response
OE send a DriverCheckDependency event and insert type-topping parameter into its privateState:

```json
{
  "sessionContext": {
    "privateState": {
      "outcomeEngine": {
        "customerDataStore": {
          "checkingDependency": {
            "type-topping": true
          }
        }
      }
    },
    "dialogState": {
      "agentsWithValue": {
        "pizzatoppings": {
          "value": "sausage"
        },
        "pizzatype": {
          "status": "CONFIRMED",
          "value": "vegetarian"
        }
      }
    }
  },
  "events": [
    {
      "name": "DriverCheckDependency",
      "params": {
        "uids": [
          "pizzatype",
          "pizzatoppings"
        ]
      }
    }
  ],
  "logs": []
}
```

###2.a CE-request
NCE send a Promt event to OE for pizzatoppings if CHECK type:

```json
{
    "sessionContext": {
        "privateState": {
           "outcomeEngine": {
                "customerDataStore": {
                    "checkingDependency": {
                        "type-topping": true
                    }
                }
            }
        },
        "dialogState":{
         "agentsWithValue":{
                  "pizzatoppings": {
                      "value": "sausage"
                  },
                  "pizzatype": {
                      "status": "CONFIRMED",
                      "value": "vegetarian"
                  }
         }
      }
    },
   "model": {
     "name": "{name}",
     "version": "{version}",
     "url": "{url}"
    },
    "events": [
        {
            "name": "Prompt",
            "params": {
                "uid": "pizzatoppings",
                "type": "CHECK"
            }
        },
        {
            "params": {},
            "name": "FinalizeInteraction"
        }
    ]
}
```

###2.b OE-response
OE send aN Outcome event:

```json
{
  "sessionContext": {
    "privateState": {
      "outcomeEngine": {
        "customerDataStore": {
          "checkingDependency": {
            "type-topping": true
          }
        }
      }
    },
    "dialogState": {
      "agentsWithValue": {
        "pizzatoppings": {
          "value": "sausage"
        },
        "pizzatype": {
          "status": "CONFIRMED",
          "value": "vegetarian"
        }
      }
    }
  },
  "events": [
    {
      "name": "Outcome",
      "params": {
        "text": "Are you sure you want a vegetarian pizza with sausage?"
      }
    }
  ],
  "logs": []
}
```

###3.a CE-request
CE send again an agentCollected event to OE for pizzatoppings, but this time we have type-topping parameter to true in OE privateState:

```json
{
    "sessionContext": {
        "privateState": {
           "outcomeEngine": {
                "customerDataStore": {
                    "checkingDependency": {
                        "type-topping": true
                    }
                }
            }
        },
        "dialogState":{
         "agentsWithValue":{
                  "pizzatoppings": {
                      "value": "sausage"
                  },
                  "pizzatype": {
                      "status": "CONFIRMED",
                      "value": "awesome"
                  }
         }
        }
    },
   "model": {
     "name": "{name}",
     "version": "{version}",
     "url": "{url}"
    },
    "events": [
        {
            "name": "AgentCollected",
            "params": {
               "uid": "pizzatoppings"
            }
        }
    ]
}
```

###3.b OE-response
OE send a DriverStopDependency event and delete type-topping parameter from its privateState:

```json
{
  "sessionContext": {
    "privateState": {
      "outcomeEngine": {
        "customerDataStore": {}
      }
    },
    "dialogState": {
      "agentsWithValue": {
        "pizzatoppings": {
          "value": "sausage"
        },
        "pizzatype": {
          "status": "CONFIRMED",
          "value": "awesome"
        }
      }
    }
  },
  "events": [
    {
      "name": "DriverStopDependency",
      "params": {}
    }
  ],
  "logs": []
}
```

###4.a CE-request
CE send for the third time an agentCollected event to OE for pizzatoppings.
This time the status of both the agent is CONFIRMED:

```json
{
    "sessionContext": {
        "privateState": {
           "outcomeEngine": {}
        },
        "dialogState":{
         "agentsWithValue":{
                  "pizzatoppings": {
                      "status": "CONFIRMED",
                      "value": "sausage"
                  },
                  "pizzatype": {
                      "status": "CONFIRMED",
                      "value": "vegetarian"
                  }
         }
        }
    },
   "model": {
     "name": "{name}",
     "version": "{version}",
     "url": "{url}"
    },
    "events": [
        {
            "name": "AgentCollected",
            "params": {
               "uid": "pizzatoppings"
            }
        }
    ]
}
```

###4.b OE-response
OE has not an event associated to this situation:

```json
{
  "sessionContext": {
    "privateState": {
      "outcomeEngine": {
        "customerDataStore": {}
      }
    },
    "dialogState": {
      "agentsWithValue": {
        "pizzatoppings": {
          "status": "CONFIRMED",
          "value": "sausage"
        },
        "pizzatype": {
          "status": "CONFIRMED",
          "value": "vegetarian"
        }
      }
    }
  },
  "events": [],
  "logs": []
}
```
