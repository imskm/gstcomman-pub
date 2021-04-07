# GST-COMMAN
Documentation for **GST-COMMAN**

## APIs
All the routes should be prefixed with `/api/v1`, so for example the first route should finally be `/api/v1/auth/register`.
| Method   | API                             		| Description 																|
| :-------:| ---------------------------------		| ----------- 																|
| `POST`   | /auth/register 						| User registerarion 														|
| `POST`   | /auth/login 							| User login 																|
| `GET`    | /auth/logout 							| User logout 																|
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
| `GET`    | /payment/apikey 						| Returns Razorpay Key ID (needed for checkout page in client side)			|
| `GET`    | /payment/verify 						| Verify the payment authenticity											|
| `GET`    | /payment/failure 						| Notify server about the failed payment 									|
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
| `GET`    | /my/order/index 						| Show all orders 															|
| `GET`    | /my/order/show 						| Show single orders 														|
| `POST`   | /my/order/store 						| Create new order (purchase of package (silver, premium)					|
| `POST`   | /my/profile/update 					| Update user profile details												|
| `POST`   | /my/profile/update-photo 				| Update user profile picture												|
| `POST`   | /my/profile/verify-phone 				| Verify phone, an OTP will be sent to this number 							|
| `POST`   | /my/profile/update-phone 				| Update user phone number (takes OTP, and phone) 							|
| `GET`    | .. 									| @TODO API for push notification (subscribe/unsubscribe). 					|

*Note*: All index apis should implement pagination

## User APIs documentation
Some global request response rules
* All `*/store` request (i.e. create resource) on successful, will respond with status `201` and return JSON object of created resource.
* All `*/index` request (i.e. fetch resource) on successful, will respond with status `200` and return JSON array of resources (it can be empty).
* All `*/show` request (i.e. fetch single resource) on successful, will respond with status `200` and return JSON object of that resource,
  `404` (NOT FOUND) if resource does not exist
* All `*/delete` request (i.e. delete single resource) on successful, will respond with status `200`,
  `404` (NOT FOUND) if resource does not exist.
* All `*/update` request (i.e. update single resource) on successful, will respond with status `200`,
  `404` (NOT FOUND) if resource does not exist.
* On validation error of any request will respond with status `422` and return JSON error object (error key can be `null`):
```javascript
{
	"status": "error",
	"msg": "One ore more error(s) in form",
	"error": {
		"first_name": "Invalid first name",
		"last_name": "Invalid last name",
	}
}
```
* On `500` INTERNAL SERVER ERROR, the response body will be
```javascript
{
	"status": "error",
	"msg": "Something bad happend. Please try again later",
	"error": null
}
```

`error` property can be `null`. If `error` property is not `null` then it will contain list of properties with error message as value of that property,
those property represents field name in the submitted form. This error message most likely be generated on the api which accepts data from fornt end and
stores it in database after validation. So the error message represents validation errors.

* All other APIs will respond according to RESTful APIs


###  Register new user
`/api/v1/auth/register`

```console
curl -X POST http://127.0.0.1:8888/api/v1/auth/register \
	-d "first_name=demo&last_name=user&gender=1&phone=9881924450&gst_number=XYZ1235671"
```
**Response** `201` on successful `422` on validation error, `500` on internal server error

### Login
`/api/v1/auth/login` hitting this api will generate OTP and send it to POSTed phone number.

```console
curl -X POST -d "phone=9881924450" http://127.0.0.1:8888/api/v1/auth/login
```
**Response** `200` on successful `422` on validation error, `500` on internal server error
An OTP will be sent to give phone number. For the testing purpose OTP is `019283`.
**NOTE**: If you get response code `422` then hit this api `curl -X POST -d "otp=019283" http://127.0.0.1:8888/api/v1/auth/verify`.
As the OTP is hard coded so many time you will get `422`, so just verify OTP then OTP will be cleared from db and you will be good to go.

### Verify OTP for login
`/api/v1/auth/verify` htting the api will verify the OTP and authenticates the user

```console
curl -X POST -d "otp=019283" http://127.0.0.1:8888/api/v1/auth/verify
```
**Response** `200` on successful `422` on validation error, `500` on internal server error
```javascript
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

### Create new bill (upload bill)
`/api/v1/my/bill/store` hitting this api will create new bill for logged in user
```console
curl \
	-F "receipt_type_id=1" \
	-F "description=sample description" \
	-F "user_id=1" \
	-F "receipt=@temp/demo-bill.jpg" \
	--cookie "APIKEY=KUIvbOLR359YM7jJOZaeemIN/890" \
	"http://127.0.0.1:8888/api/v1/bill/store"

```
**Response** `201` on successful `422` on validation error, `500` on internal server error


### Update existing bill (can upload bill)
`/api/v1/my/bill/update` hitting this api will update existing bill for logged in user
```console
curl \
	-F "id=1" \
	-F "receipt_type_id=1" \
	-F "description=sample description" \
	-F "user_id=1" \
	-F "receipt=@temp/demo-bill.jpg" \
	--cookie "APIKEY=KUIvbOLR359YM7jJOZaeemIN/890" \
	"http://127.0.0.1:8888/api/v1/bill/update"

```
**Response** `201` on successful, `404` on not found, `422` on validation error, `500` on internal server error

When trying to update bill which does not exist in DB then response status will be `404`.


### Delete existing bill 
`/api/v1/my/bill/delete?id=123` hitting this api will delete bill with id 123 for logged in user
```console
curl \
	--cookie "APIKEY=KUIvbOLR359YM7jJOZaeemIN/890" \
	-X DELETE "http://127.0.0.1:8888/api/v1/bill/delete"
```
**Response** `200` on successful, `404` on not found, `422` on validation error, `500` on internal server error


### Place order (user buys package)
`/api/v1/my/order/store` hitting this api create order with status `initiated` or `created`
```console
curl \
	-X POST \
	-d "user_id=2&subscription_id=1" \
	--cookie "APIKEY=KUIvbOLR359YM7jJOZaeemIN/8ZL" \
	"http://127.0.0.1:8888/api/v1/my/order/store"

```
**Response** `201` on successful, `404` on not found, `422` on validation error, `500` on internal server error

Response body is `order` entity, please refer to SQL


### Update user profile basic details
`/api/v1/my/profile/update` hitting this api will update user profile details such as name, gender etc.
```console
curl \
	-X POST \
	-d "first_name=Shek&last_name=Mukhtar&gender=2" \
	--cookie "APIKEY=KUIvbOLR359YM7jJOZaeemIN/8ZL" \
	"http://127.0.0.1:8888/api/v1/my/profile/update"

```
**Response** `200` on successful, `422` on validation error, `500` on internal server error

### Change registered phone number (Step 1)
`/api/v1/my/profile/verify-phone` hitting this api will send OTP to POSTed phone number
```console
curl \
	-X POST \
	-d "phone=9819000000" \
	--cookie "APIKEY=KUIvbOLR359YM7jJOZaeemIN/8ZL" \
	"http://127.0.0.1:8888/api/v1/my/profile/verify-phone"

```
**Response** `200` on successful, `422` on validation error, `500` on internal server error

### Change registered phone number (Step 2)
`/api/v1/my/profile/update-phone` hitting this api will change registered phone number to new number (if everything is OK)
```console
curl \
	-X POST \
	-d "phone=9819000000&otp=019233" \
	--cookie "APIKEY=KUIvbOLR359YM7jJOZaeemIN/8ZL" \
	"http://127.0.0.1:8888/api/v1/my/profile/update-phone"

```
**Response** `200` on successful, `422` on validation error, `500` on internal server error





