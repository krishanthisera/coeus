---
title: "Perl Db Transaction"
date: 2020-12-16T15:20:43+10:30
draft: true
author: "Krishan Thisera"
---

![perl-mysql-banner](https://i.ibb.co/BjXBSBG/Presentation1-1-300x252.png)


_Note that the original post can be found in [blog.bizkt.com.au](https://blog.bizkt.com.au/2018/05/14/perl-database-transactions/) and the content of the post has *not been update* by the author._ 

To understand what is a *database transaction* lets look at a simple bank withdrawal and deposit scenario.
Assume that you have two bank accounts call A and B, and you need withdraw some amount from account A and Deposit it on account B.
In this scenario what happen if you couldn't withdraw money from your account A, the deposit part won't carry out.
Again if you couldn't deposit the money to your account B you have to deposit them back on account A (Which means a roll back).
So in the  context of Database, Transaction is refer to  a sequence of jobs which is supposed to run as a whole. So in other words, it should happen as whole or not.
So as in our following example, we have 3 Database queries which should perform as a whole. Further assume that our first Database query is supposed to perform a insert if successful, second query  should update a table if successful, third query  should delete an entry from a table.  So if any of them fails due to any circumstances everything should rollback to initial state.
Anyway the concept behind the Transaction is your data will be in sensible state no matter what happens.
There are four types of properties the context of Database transaction (know as ACID).
1. Atomic :  If one part of the transaction fails, then the entire transaction fails, and the database state is left unchanged.
2. Constraints : Only valid changes will be accepted, If changes are invalid system leave with the previous state.
3. Isolated : Until transactions are committed no one else is supposed to see the changes.
Durable : Once a valid change is committed, it will remain so.  
You can find a pretty good explanation about ACID in here.
Okay now lets looks in to our perl example.
First of all lets install perl-mysql dependencies for our Debian system

`#apt-get install libdbd-mysql-perl`

Afterwards let's install the DBI module for perl,

```sh
$cpan 
>install DBI
```

Now let's look into our Perl script, (Following content should reside on your Perl script)
I have used a hash to store database configuration (MYSQL), and another Hash to store connection attributes (ATTRIB) (You can also store these attributes on MYSQL hash, depends on your requirement)

```sh
#!/usr/bin/perl
use strict;                                                              
use warnings;                                                       
use DBI;                     #Including DBI module for databases



my %MYSQL = (
         hostname   =>  "localhost",
         username   =>  "root",
         password   =>  "password",
         database   =>  "customers"
);
my %ATTRIB = (RaiseError  =>  1,      #Enable error handling
              AutoCommit  =>  0       #Enabling Transactions
);
```

Now we shall establish our Databases connections (Since its so obvious I'm not going to explain database connection string),

```sh
my $DB_CON = DBI->connect("dbi:mysql:$MYSQL{database}:$MYSQL{hostname}","$MYSQL{username}","$MYSQL{password}",%ATTRIB) || die("Couldn't connect to the Database!n");
```

Now let's execute our sample transaction (In the following example we wrap our DB quires inside a transaction),
In the following example, I've use eval function, Generally, eval interprets a string as code further eval lets a perl program run a perl code within itself.

```sh
eval {

#Update quarry 
 $SQL_STR = "UPDATE cus_info SET cus_tp='$CUS_TP' WHERE cus_id=$CUS_ID";
        $SQL_EXEC = $DB_CON->prepare($SQL_STR);
 $SQL_EXEC->execute();
 print "Update Quarry Executedn";

#Insert quarry 
 $SQL_STR = "INSERT INTO product_main(prod_name,prod_stock) VALUES ('$PRODUCT_NAME','$PRODUCT_STOCK')";
        $SQL_EXEC = $DB_CON->prepare($SQL_STR);
        $SQL_EXEC->execute();
        print "Insert Quarry Executedn";

#Delete quarry
 $SQL_STR = "DELETE FROM product_info WHERE prod_id='$PRODUCT_ID'";
        $SQL_EXEC = $DB_CON->prepare($SQL_STR);
        $SQL_EXEC->execute();
        print "Delete Quarry Executedn";

#Commit If everything went well
 $DB_CON->commit();
};
```

According to our scenario, if there is any unexpected behaviour occurred, it should rollback any changes and database should keep the previous state.

```sh
if($@){ 
    print "Transactions were Rolled Back"; $DB_CON->rollback(); 
}
```
PS:  '$@' will set if our eval did not compile. Please click here for further information from perl docs.
Now lets close our connection to the database,

`$DB_CON->disconnect();`

Click [here](https://drive.google.com/file/d/1EVvZb9R_2_ve3NEmmKqAIun2JpdNPE7a/view?usp=sharing) to download the sample program including a database dump.

Cheers
