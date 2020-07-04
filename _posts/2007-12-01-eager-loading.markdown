---
layout: post
title:  "Eager Loading"
date: 2007-12-01 12:00:00 -0600
categories: 
---

One thing that made me uneasy about Rails&#8217; ActiveRecord was how it handles associations.  For instance, say I have a table of products in the database and another table listing comments about the products.  So, for each product we have many comments.

Back in the good ol&#8217; ColdFusion days, I would write a query that joins the two tables.  Then I would loop through each field and have some funky conditional statements, like the following:

```
<cfset prevProductId = 0 />
<cfloop query="qryProductsWithComments">
 <cfif product_id neq prevProductId>
  ...if this is not the first product, finish printing the previous product's template...
  ...we've hit either the first product or a new product, so print out the beginning of the product template and the first comment...
 <cfelse>
  ...this is another comment, so print out just another comment...
 </cfif>
 <cfset prevProductId = product_id />
</cfloop>
```

That&#8217;s pretty darn confusing, but it was better, performance-wise, than executing a new query every time you iterate over the products loop, like so:

```
<cfloop query="qryProducts">
 ...spit out the product template...
 <cfquery name="qryComments" ...>
  SELECT * FROM comments WHERE product_id = #qryProducts.product_id#
 </cfquery>
 <cfloop query="qryComments">
  ...spit out a comment...
 </cfloop>
</cfloop>
```

By default, ActiveRecord will perform similarly to the latter example.  However, with eager loading, you can get the same performance as the former example without all of the confusing if statements:

in our model:

```
class Product < ActiveRecord::Base
 has_many :comments
end
```

in our controller:

```
<p>class ProductsController < ApplicationController
 def index
  @products = Product.find(:all, :include => :comments)
 end
end
```

in our view:

```
<% for product in @products -%>
 <%= render :partial => "products/product" %>
 <% for comment in @products.comments -%>
  <%= render :partial => "comments/comment" %>
 <% end -%>
<% end -%>
```

So much cleaner!  And easier!

With ASP.NET 2.0, you&#8217;re pretty much stuck doing the equivalent of the second CF example using the server controls or you have to build your own object data source that will work similarly to ActiveRecord, but you&#8217;ve just spent an hour or so reinventing the wheel.
