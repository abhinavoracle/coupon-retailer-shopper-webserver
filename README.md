<p align="center">
    <img src="https://user-images.githubusercontent.com/1342803/43925087-512bb1de-9bf4-11e8-869f-057af9afadb9.png" width="320" alt="Auth Template">
    <br>
    <br>
    <a href="http://docs.vapor.codes/3.0/">
        <img src="http://img.shields.io/badge/read_the-docs-2196f3.svg" alt="Documentation">
    </a>
    <a href="https://discord.gg/vapor">
        <img src="https://img.shields.io/discord/431917998102675485.svg" alt="Team Chat">
    </a>
    <a href="LICENSE">
        <img src="http://img.shields.io/badge/license-MIT-brightgreen.svg" alt="MIT License">
    </a>
    <a href="https://circleci.com/gh/vapor/auth-template">
        <img src="https://circleci.com/gh/vapor/auth-template.svg?style=shield" alt="Continuous Integration">
    </a>
    <a href="https://swift.org">
        <img src="http://img.shields.io/badge/swift-4.1-brightgreen.svg" alt="Swift 4.1">
    </a>
</p>

To understand how this API works, please take a look at Sources/App/routes.swift.

As far as this backend service and API is concerned, there are exactly 3 kinds of users: Shopper, Retailer, CouponIssuer. Each of these three users has his/her own login credentials. Ideally, the Shopper will login through a shopper-facing app, the Retailer will login through a retailer-facing app (possibly just another login page within the same as as the shopper, but leading to a different UX), and there will only be one CouponIssuer, which will be another backend server which can accept HTTP POST requests from this API and which can send its own HTTP POST requests to this API.

Basically, this is how a common interaction with this REST API would go down (in a first iteration/simple design -- no webhooks yet):

1) Shopper creates an account.

2) Shopper logs in.

3) Shopper POSTs his location to this backend via this API.

3.5) Shopper may also update his preferences/interests and also POSTs this data to this API.

4) This backend receives the shopper data and sends back a response to the shopper client to keep it from hanging.

5) This backend sends a POST of the shopper data to the backend REST API that the CouponIssuer has access to.

6) The CouponIssuer acknowledges that it recieved the shopper data to this API, to keep this API from listening for a response.

7) CouponIssuer analyzes the data and figures out which coupons are relevant to the particular shopper, given the shopper data.

8) CouponIssuer sends the relevant coupons back to this API via a POST.

9) This API lets the CouponIssuer know that it received the relevant coupons for the particular shopper.

10) All the while, during some interval from when the shopper originally sent its last piece of data, the shopper client has been continually polling, with HTTP GET, for relevant coupons.

10.5) The particular shopper recieves his relevant coupons.

11) At some later point in time, the shopper decides to use one of the coupons... here's where the retailer client comes into the picture.

12) The participating Retailer scans or punches in shopper coupon code at checkout. The fact that this coupon is pinged at checkout gets POSTed to this API.

13) Finally, based on validity and terms of the coupon, the shopper will be rewarded accordingly (for instance, based on some progress, if that progress is achieved by the shopper, and while the coupon is not yet expired, the shopper will be refunded the ammount/percentage/points specified in the coupon -- post-POS/transaction with the participating retailer).


Note: In case I have not yet deleted the following files, please ignore them and references to the classes defined within them, as they were generated with the vapor template I used, and I just kept them around in the first couple commits as a reference-to-self on how backend Swift and the Vapor library can be used with auth and a db. Please ignore the following files:

Sources/App/Controllers/UserController.swift

Sources/App/Controllers/TodoController.swift

Sources/App/Models/User.swift

Sources/App/Models/UserToken.swift

Sources/App/Models/Todo.swift