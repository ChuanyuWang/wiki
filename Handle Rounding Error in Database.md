> Best answer from http://stackoverflow.com/questions/11541939/mongodb-what-about-decimal-type-of-value

If you want an exact representation for financial purposes, then doubles or floating point values are unsuitable as the fractional parts are subject to rounding error. Certain decimal values cannot not be represented using binary-based floating points and must be approximated.

For a less technical intro, see [The trouble with rounding floating point numbers](http://www.theregister.co.uk/2006/08/12/floating_point_approximation/); if you want to geek out, then read [What Every Computer Scientist Should Know About Floating-Point Arithmetic](http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html).

The recommendation of using an integer type (storing the value in cents) is to avoid potential rounding errors. This approach is described as "[Using a Scale Factor](https://docs.mongodb.com/manual/tutorial/model-monetary-data/#using-a-scale-factor)" in the MongoDB documentation for [modelling monetary data](https://docs.mongodb.com/manual/tutorial/model-monetary-data/) and is a general workaround for MongoDB 3.2 and earlier.

MongoDB 3.4 includes a new [Decimal BSON type](https://docs.mongodb.com/manual/tutorial/model-monetary-data/#numeric-decimal) which provides exact precision for manipulating monetary data fields.

Support for decimals in e-commerce or financial applications is very important. Using integers is a hack at best. Different currencies use a different number of decimal digits so it's impossible just assume that you can use an integer with the last 2 or 4 digits denoting decimals. If in doubt, just google for [Knight Capital](http://en.wikipedia.org/wiki/Knight_Capital_Group#2012_stock_trading_disruption), the company that lost $440M in 45 minutes because of a bug with their integer representation
