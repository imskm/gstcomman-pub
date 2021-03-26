# GST-COMMAN
Documentation for **GST-COMMAN**

## APIs
All the routes should be prefixed with `/api/v1`, so for example the first route should finally be `/api/v1/auth/register`.
| Method   | API                             		| Description 																|
| :-------:| ---------------------------------		| ----------- 																|
| `POST`   | /auth/register 						| User registerarion 														|
| `POST`   | /auth/login 							| User login 																|
| `GET`    | /auth/logout 							| User logout 																|
| `POST`   | /auth/recover 							| User recovers password when forgets password								|
| `POST`   | /auth/verify 							| Verify OTP (for OTP based login)											|
| 		   |										| 																			|
| `GET`    | /bill/index 							| Show all bills (filter: processed, un-processed, month, bill\_type)		|
| `GET`    | /bill/show?id=bill\_id					| Show bill by bill id 														|
| `POST`   | /bill/store 							| Upload new receipt (purchase/sales/cash sheet/etc. bill) 					|
| `POST`   | /bill/update 							| Update existing un-processed bill (purchase/sales/cash sheet/etc. bill) 	|
| `DELETE` | /bill/delete 							| Delete existing un-processed bill (purchase/sales/cash sheet/etc. bill) 	|
| `GET`    | /bill/stats 							| Returns statistics in which admin is interested (filter: month, etc.		|
| `POST`   | /bill/mark-accounted 					| POST request to mark the bill as accounted (update bill status) 			|
|          |										| 																			|
| `GET`    | /user/index 							| Show all users (filter: applies)											|
| `GET`    | /user/show?id=user\_id					| Show single user by bill id 												|
| `POST`   | /user/store 							| Create new user										 					|
| `POST`   | /user/update 							| Update existing user 														|
| `DELETE` | /user/delete 							| Delete existing user														|
| `GET`    | /user/stats 							| Returns statistics in which admin is interested (filter: month, etc.		|
| `POST`   | /user/gst-collected 					| POST request to mark the GST payment collection status as paid			|
|          |										| 																			|
| `POST`   | /gst-countdown/start 					| POST request to start the count down of GST filing date is near			|
| `POST`   | /gst-countdown/stop 					| POST request to stop the count down of GST filing date 					|
|          |										| 																			|
| `GET`    | /subscription/index					| Show all available subscription 											|
| `GET`    | /subscription/show?id=subs\_id			| Show single subscription of given service id 								|
| `POST`   | /subscription/store					| Create new subscription 													|
| `POST`   | /subscription/update					| Update existing subscription (delete is not allowed)						|
| `POST`   | /subscription/puchase					| Purchase any of the subscriptions (starter, economic, premium)			|
|          |										| 																			|
| `GET`    | /order/index							| Show all orders (accessible to admin), (order cancellation not allowed)	|
| `GET`    | /order/show?id=orderid					| Show single order (accessible to admin)									|
| `GET`    | /order/stats 							| Returns statistics in which admin is interested (filter: month, etc.		|
|          |										| 																			|
| `GET`    | /payment/index							| Show all payments (accessible to admin), (order cancellation not allowed)	|
| `GET`    | /payment/show?id=paymentid				| Show single payment (accessible to admin)									|
| `GET`    | /payment/stats 						| Returns statistics in which admin is interested (filter: month, etc.		|
|          |										| 																			|
| `GET`    | /my/order/index						| Show all orders (accessible to user), (filter: month)						|
| `GET`    | /my/order/show?id=orderid				| Show single order (accessible to user)									|
|          |										| 																			|
| `GET`    | /my/bill/index							| Show all bills (filter: (un-)processed, month, bill\_type)				|
| `GET`    | /my/bill/show?id=billid				| Show single bill 															|
| `POST`   | /my/bill/store 						| Upload new receipt (purchase/sales/cash sheet/etc. bill) 					|
| `POST`   | /my/bill/update 						| Update existing un-processed bill (purchase/sales/cash sheet/etc. bill) 	|
| `DELETE` | /my/bill/delete 						| Delete existing un-processed bill (purchase/sales/cash sheet/etc. bill) 	|
| `GET`    | /my/bill/stats 						| Returns statistics in which admin is interested (filter can be added) 	|
| `POST`   | /my/profile/update 					| Update user profile details												|
| `POST`   | /my/profile/update-photo 				| Update user profile picture												|
| `POST`   | /my/profile/verify-phone 				| Verify phone, an OTP will be sent to this number 							|
| `POST`   | /my/profile/update-phone 				| Update user phone number (takes OTP, and phone) 							|
| `GET`    | .. 									| @TODO API for push notification (subscribe/unsubscribe). 					|

*Note*: All index apis should implement pagination

## User APIs documentation

1. `/api/v1/auth/register`
```console
curl -X POST http://127.0.0.1:8888/api/v1/auth/register \
	-d "first_name=demo&last_name=user&gender=1&phone=9881924450&gst_number=XYZ1235671"
```
**Response** `201` on successful `422` on validation error, `500` on internal server error

2. `/api/v1/auth/login`
```console
curl -X POST -d "phone=9881924450" http://127.0.0.1:8888/api/v1/auth/login
```
**Response** `200` on successful `422` on validation error, `500` on internal server error
An OTP will be sent to give phone number. For the testing purpose OTP is `019283`.
**NOTE**: If you get response code `422` then hit this api `curl -X POST -d "otp=019283" http://127.0.0.1:8888/api/v1/auth/verify`.
As the OTP is hard coded so many time you will get `422`, so just verify OTP then OTP will be cleared from db and you will be good to go.

3. `/api/v1/auth/verify`
```console
curl -X POST -d "otp=019283" http://127.0.0.1:8888/api/v1/auth/verify
```
**Response** `200` on successful `422` on validation error, `500` on internal server error
```json
{
	"id":2,
	"first_name":"shek",
	"last_name":"muktar",
	"gender":1,
	"phone":"9881924450",
	"gst_number":"XYZ1235671",
	"photo":null,
	"is_verified":false,
	"is_active":false,
	"created_at":"2021-03-21T16:26:43.998749",
	"updated_at":"2021-03-21T16:26:43.998749"
}
```

All `/store` api call will return created resource in JSON and status will be `201` (created).
All `422` response will always returns JSON representing error in response body
```json
{
	"status": "error",
	"msg": "Error(s) in the form",
	"error": {
		"phone":"Invalid phone"
	}
}
```
here `error` property can be `null`. If `error` property is not `null` then it will contain list of properties with error message as value of that property,
those property represents field name in the submitted form. This error message most likely be generated on the api which accepts data from fornt end and
stores it in database after validation. So the error message represents validation errors.

All `500` response will always contains same error structure as in `422` response. Value of `error` can be `null`.
All other kind of response status does not include any response body, the status code express the reason.
