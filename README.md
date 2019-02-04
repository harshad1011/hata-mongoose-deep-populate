Mongoose plugin to enable population of nested models at any level of depth. Support **Mongoose 3.8.x** and later. Refer to [changelog](https://github.com/harshad1011/hata-mongoose-deep-populate#changelog) for breaking changes.
(https://www.npmjs.com/package/hata-mongoose-deep-populate)

You'll need to write your own getModel() function so as to set database connection.
This NPM returns populated records based on the model passed.

### Usage

Sample usages are based on the following schemas:

```javascript
var UserSchema = new Schema({})

var CommentSchema = new Schema({
  user  : {type: Number, ref: 'User'}
})

var PostSchema = new Schema({
  user    : {type: Number, ref: 'User'},
  comments: [{type: Number, ref: 'Comment'}],
  likes   : [{user: {type: Number, ref: 'User'}}],
  approved: {status: Boolean, user: {type: Number, ref: 'User'}}
})
```

#### Register plugin

```javascript
// CHANGE from 1.x: need to pass in mongoose instance
var deepPopulate = require('hata-mongoose-deep-populate')(mongoose);
PostSchema.plugin(deepPopulate, options /* more on options below */);
```

#### Perform population

On `Post` model:

```javascript

// Use callback
Post.deepPopulate(posts, [{path: 'comments.user', model: [Comment.getModel(id), Usere.getModel(id)]}], function (err, _posts) {
  // _posts is the same instance as posts and provided for convenience
  posts.forEach(function (post) {
    // post.comments and post.comments.user are fully populated
  });
});

// Use promise
Post.deepPopulate(posts, [{path: 'comments.user', model: [Comment.getModel(id), User.getModel(id)]}]).then(...);
```

On an instance of `Post`:

```javascript
// Use callback
post.deepPopulate([{path: 'comments.user', model: [Comment.getModel(id), User.getModel(id)]}], function (err, _post) {
  // _post is the same instance as post and provided for convenience
});


// Use promise
post.deepPopulate([{path: 'comments.user', model: [Comment.getModel(id), User.getModel(id)]}]).then(...);
```

On `Query` (returns the same query object to chain):

```javascript
Post.find().deepPopulate([{path: 'comments.user', model: [Comment.getModel(id), User.getModel(id)]}]).exec(function (err, posts) { ... });
Post.findOne().deepPopulate([{path: 'comments.user', model: [Comment.getModel(id), User.getModel(id)]}]).exec(function (err, post) { ... });
Post.findById(id).deepPopulate([{path: 'comments.user', model: [Comment.getModel(id), User.getModel(id)]}]).exec(function (err, post) { ... });
```


#### Populate multiple paths

```javascript
post.deepPopulate([{path: 'user', model: [User.getModel(id)]}, {path: 'comments.user', model: [Comment.getModel(id), User.getModel(id)]}, {path: 'likes.user', model: [null, User.getModel(id)]}, {path: 'approved.user', model: [null, User.getModel(id)]}], cb);
```


### Test

The test suite will **drop the database** each run, so only run it against a test database. To run tests, execute this command where `--db` is the connection string.

```
gulp test --db mongodb://127.0.0.1/mongoose_deep_populate_test_db
```

### Changelog

#### v0.0.1

* Initial release


### License

MIT
