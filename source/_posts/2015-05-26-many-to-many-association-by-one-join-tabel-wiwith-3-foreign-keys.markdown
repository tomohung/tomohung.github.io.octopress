---
layout: post
title: "Many to Many Association by One Join Tabel with 3 Foreign keys"
date: 2015-05-26 14:35:28 +0800
comments: true
categories: 
---

延續[上一篇問題](http://www.tomohung.com/blog/2015/05/25/many-to-many-association-with-two-join-tables/)

建立滿足以下關係的Table Associations

1. User can create a tag for a book.
2. According to a given tag, return book list.
3. According to a given book, return tag list.

----------

這次只用一個Join Table但包含了3個FK來實作

建立4個Table

```ruby schema
  create_table "books", force: :cascade do |t|
    t.string "title"
  end

  create_table "tags", force: :cascade do |t|
    t.text "label"
  end

  create_table "user_book_tags", force: :cascade do |t|
    t.integer "user_id"
    t.integer "book_id"
    t.integer "tag_id"
  end

  create_table "users", force: :cascade do |t|
    t.string "name"
  end
```

其中user_book_tags包含了`user_id`, `book_id`, `tag_id`，3個Foreign key

設定Model Associations


```ruby User
class User < ActiveRecord::Base
  has_many :user_book_tags
  has_many :tags, through: :user_book_tags
end
```

```ruby Book
class Book < ActiveRecord::Base
  has_many :user_book_tags
  has_many :tags, through: :user_book_tags
end
```

```ruby Tag
class Tag < ActiveRecord::Base
  has_many :user_book_tags
  has_many :users, through: :user_book_tags

  has_many :books, through: :user_book_tags
end
```

```ruby UserBookTag
class UserBookTag < ActiveRecord::Base
  belongs_to :user
  belongs_to :book
  belongs_to :tag
end
```

再來是在Rails Console裡測試

```ruby
# create data
> User.create(name: "Tomo")
> Book.create(title: "Ruby")
> Tag.create(label: "good book")
> UserBookTag.create(user: User.first, book: Book.first, tag: Tag.first)

# test associations
> User.first.tags
> Book.first.tags
> Tag.first.users
> Tag.first.books
```

與[上一次的方法](http://www.tomohung.com/blog/2015/05/25/many-to-many-association-with-two-join-tables/)相比，少建立一個Table。