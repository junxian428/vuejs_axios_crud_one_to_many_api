npm install vue-cookies

npm install axios

npm run serve

npm install --save vue-plugin-load-script@^2.x.x

ERD Diagram

![Untitled Diagram drawio (10)](https://github.com/junxian428/vuejs_frontend_crud/assets/58724748/4ac07240-4f38-4e91-9d83-62488ed8e8fa)


Login Page:

![image](https://github.com/junxian428/vuejs_frontend_crud/assets/58724748/7567d67e-167e-47e5-9460-998af5e5a94f)

Main:

Date: 27/7/2023 

Time: 4:00 pm

![image](https://github.com/junxian428/vuejs_frontend_crud/assets/58724748/a6fcab61-8dde-4ccd-807a-d99c7d56d4d5)



CSS Design (Login Page)

https://enlear.academy/31-example-login-form-for-website-42abc6f6d525


31/7/ 2023 - Delete Function (PLC and Address but have bug)

1/8/ 2023 - 

POST METHOD: localhost:8089/plc

{
    "name": "PLC",
    "token": "UWU",
    "userid": "1",
        "addresses": [
            {
                "name": "Address 1",
                "description": "Description for Address 1 in PLC 6"
            },
            {
                "name": "Address 3",
                "description": "Description for Address 3 in PLC 6"
            },
            {
                "name": "Address 3",
                "description": "Description for Address 3 in PLC 6"
            }
        ]

}


PUT METHOD: localhost:8089/plc/34 (Frontend Add method)


{
    "name": "PLC",
    "token": "TTT",
    "userid": "1",
    "addresses": [
        {
            "name": "XDD",
            "description": "Description for Address 3 in PLC 6"
        }
    
    ]

}


DELETE METHOD: localhost:8089/plc/10


DELETE METHOD: localhost:8089/address/97



