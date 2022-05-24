# Difficulties Faced

1)	Using the same libraries more than once in the same code might not work. This issue can be resolved by creating different instances for each method alone.
2)	Joining the pubsubclient library for azure and the telegram code causes some errors. This is due to the security of the pubsubclient library (the fingerprint) which wouldn’t allow the insecure connection of the telegram. This issue can be resolved by disconnecting the pubsubclient after using it in order for the telegram code to run.
3)	The default timestamp on AWS DynamoDB (which consists of 13 digits) has a different format than that of firebase and Azure MySQL Database (which consist of 10 digits).
4)	If you want to create a new AWS DynamoDB, you should change the name of the ‘outtopic’.
5)	There is no easy way to empty a table in the AWS DynamoDB. You are better off with deleting the whole table.
