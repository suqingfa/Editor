# method reference

https://www.mongodb.com/docs/manual/reference/method/js-collection/

# database

```
use spring;
show dbs;
db.dropDatabase();
```

# user

```
db.createUser({
user:'spring',
pwd:'spring',
roles:[{role:'readWrite',db:'spring'}]
});

db.dropUser()

show users;
show roles;
```

# collection

```
db.createCollection('xxx');

db.getCollectionInfos()

db.xxx.drop()
```

# document

```
db.xxx.insertOne({
    key1:'a',
    key2:'b'
})

db.xxx.insertMany([{
    key1:'a',
    key2:'b'
}])

db.xxx.deleteMany({})

db.xxx.find({})
db.xxx.aggregate({})

db.xxx.updateMany({})
```