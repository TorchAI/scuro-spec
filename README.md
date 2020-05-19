# scuro-spec

ScuroQL is a query language for APIs created by TorchAI. 

For more information about Scuro, please refer (here)[https://github.com/TorchAI/scuro].



## Overview

This is a Working Draft of the Specification for ScuroQL, a query language for APIs created by TorchAI.

In order to be broadly adopted, ScuroQL will have to target a wide
variety of backends, frameworks, and languages, which will necessitate a
collaborative effort across projects and organizations. This specification serves as a point of coordination for this effort.


## Getting started

ScuroQL is intuitive and easy to learn. I will try my best to explain the syntax to you.

We are going to understand and write a json request following ScuroQL syntax.

Let's get started by a example!

Say, you are developing a question-answering website/app. For a specfic page, you want to get all the users id and their questions id in a json format.

You can format the following json and sent it to Scuro server.

```
test_array_json = {
    "Dummy[]": {
        "SELECT":  ["id", 'created_at', {"ARRAY_JSON": {
                                                "ALIAS": 'user_answers',
                                                "SELECT": ['question_id', 'created_at'],
                                                "FROM": "answer",
                                                "WHERE": {
                                                    "=":
                                                        {'answer.user_id': 'user_account.id'}
                                                }
                                    }}],
        "FROM": ["user_account"]
        }
    }
```

Let's break it down.

The ScuroQL will be used accompanied with Scuro Server. The Scuro Server will return the result as you requested in a json format.

The outermost json is:

```
"Dummy[]": {}

```
Here, `Dummy` is the name of the returned result of the json inside its json body .
`[]` indicates that the json insides the {} will contains all the information of a single, independent, full SQL query.


```
"Dummy[]": {
     "SELECT":  ["id", 'created_at'],
     "FROM": ["user_account"]
 }       
 ```
`"SELECT": []` specifies the columns will be seleced.
`"FROM": ["user_account"]` tells the table to select from.

Here it will select `"id", 'created_at'` columns from `user_account` table.


```
test_array_json = {
    "Dummy[]": {
        "SELECT":  ["id", 'created_at', {"ARRAY_JSON": {
                                                "ALIAS": 'user_answers',
                                                "SELECT": ['question_id', 'created_at'],
                                                "FROM": "answer",
                                                "WHERE": {
                                                    "=":
                                                        {'answer.user_id': 'user_account.id'}
                                                }
                                    }}],
        "FROM": ["user_account"]
        }
    }
```

There is another element in `SELECT`:

```
{"ARRAY_JSON": {
                "ALIAS": 'user_answers',
                "SELECT": ['question_id', 'created_at'],
                "FROM": "answer",
                "WHERE": {
                    "=":
                        {'answer.user_id': 'user_account.id'}
 }
 ```
 
To understand it, I will explain a bit on how query is structured in ScuroQL.

In a ScuroQL query, the key will usually be regarded as a operator. And any operation will be wrapped up as a json. 

For example: 
```
 {
    "=":
        {'answer.user_id': 'user_account.id'}
}
 ```
This part represents `answer.user_id = user_account.id` in the final parsed SQL query. You can view it as: the `=` operator takes two inputs from its json body, and output the result `answer.user_id = user_account.id`

 
 Now, let's get back to the orignal query.
```
{"ARRAY_JSON": {
                "ALIAS": 'user_answers',
                "SELECT": ['question_id', 'created_at'],
                "FROM": "answer",
                "WHERE": {
                    "=":
                        {'answer.user_id': 'user_account.id'}
 }
 ```
 
 From above, we know `ARRAY_JSON` is an opearator, and it takes the elements in its json body as input parameters.
 
 `ARRAY_JSON` tells it will apply `Array_to_json(Array_agg(Row_to_json()))` operation on the inside operation.
 
 The `"ALIAS": 'user_answers'` parameters tells that the aggregated result wil be view as `user_answers`
 
 So the above json represents 
 
 ```
 SELECT array_to_json(array_agg(row_to_json(tmp))) 
 FROM   ( 
           SELECT question_id, 
                  created_at answer 
           WHERE  ( 
                     answer.user_id = user_account.id)) tmp) as user_answers
 ```
 
 
 Finally, since Scuro will return a json result back, it will add a wrapped on the parsed result `Dummy[]: {}` 

So the above ScuroQL query corresponds the following SQL:

```
SELECT row_to_json(tmp) 
FROM   (SELECT id, 
               created_at, 
               (SELECT array_to_json(array_agg(row_to_json(tmp))) 
                FROM   (SELECT question_id, 
                               created_at answer 
                        WHERE  ( answer.user_id = user_account.id )) tmp) AS 
                      user_answers 
        FROM   user_account) tmp
```





## Motivation
1. Save bandwidth

    a. Scuro can query certain fields of data, no over/under fetching

2. Clean standards

    a. common fields are pre-defined: 
        i. number of pages: count
        ii. index of page: page
        iii. total number: total

3. Type checking

    a. the type of fields will be validated

4. HTTP status codes
    
5. Decoupling with API

    Return the json body as you requested

6. Reduce the API development workflow


## Comparison with GraphQL


