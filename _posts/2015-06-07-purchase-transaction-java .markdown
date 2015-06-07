---
layout: post
title:  "A SIMPLE TRANSACTION! (JAVA)"
date:   2015-06-07 13:25:06
categories: java console
---
In this post, i describe how to implement a simple sql transaction in java. A transaction is an operation or a group of operations with depenencies. In simple terms, either none or all of the operations should execute.

Consider purchasing a piece of land. The selling party must forfeit the land.The buying party on the other hand must acquire the land, for a successful purchase transaction. Either none or both parties must act.

#Prerequisites:
 *	A **purchase** database.
 *	A **kwame_lands** table with a name field and data rows as shown below.

~~~~~~~~
		+--------+
		| name   |
		+--------+
		| odorkor|
		| tema 	 |
		+--------+
~~~~~~~~

 *	A **kwesi_lands** table with a name field and data rows as shown below.

~~~~~~~~
		+--------+
		| name   |
		+--------+
		| gbawe  |
		| mallam |
		+--------+
~~~~~~~~

The snippet below shows a land purchase transaction between kwesi and kwame from a java console application:

{% highlight java %}

package com.vive.purchase;

import java.sql.*;
import java.util.Properties;

public class Main{
    private static PreparedStatement acquireLandPreparedStatement;
    private static PreparedStatement forfeitLandPreparedStatement;

    public static void main(String[] args){
        try {
            landPurchaseTransaction("odorkor","kwesi","kwame");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public static void landPurchaseTransaction(String land, String buyer, String seller) throws SQLException {
        Connection con = null;
        Properties connectionProperties = new Properties();
        try{
            //Establish DB connection.
            connectionProperties.put("user","username");
            connectionProperties.put("password", "password");
            con = DriverManager.getConnection("jdbc:mysql://localhost:3306/purchase",connectionProperties);
            con.setAutoCommit(false);
            System.out.println("Connected to database");

            //Create SQL queries.
            acquireLandPreparedStatement = con.prepareStatement(String.format("insert into %s_lands values ('%s')",buyer,land));
            forfeitLandPreparedStatement = con.prepareStatement(String.format("delete from %s_lands where name='%s' ", seller, land));

            int hasAcquiredLand = acquireLandPreparedStatement.executeUpdate();
            int hasForfeitedLand = forfeitLandPreparedStatement.executeUpdate();

            //Verify transaction before committing changes.
            if((hasAcquiredLand != 0)&&(hasForfeitedLand != 0)){
                con.commit();
                System.out.println("Purchase transaction was a success.");
            }else{
                con.rollback();
                System.out.println("Kuluulu suspected, call 911.");
            }
        }catch(SQLException e){
            e.printStackTrace();
        }finally{
            //Close DB connections.
            if(acquireLandPreparedStatement!=null){
                acquireLandPreparedStatement.close();
            }

            if(forfeitLandPreparedStatement!=null){
                acquireLandPreparedStatement.close();
            }

            if(con!=null){
                con.setAutoCommit(true);
                con.close();
            }
        }
    }
}

{% endhighlight %}

#Output when the transaction is successful.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Connected to database
Purchase transaction was a success.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#Output when the transaction is not successful.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Connected to database
Kuluulu suspected, call 911.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Check out the [Java Docs][java-docs] on thread implementation for more details.



[java-docs]:  https://docs.oracle.com/javase/tutorial/jdbc/basics/transactions.html
