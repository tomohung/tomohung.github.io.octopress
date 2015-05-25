---
layout: post
title: "Many to Many Association with Two Join Tables"
date: 2015-05-25 21:37:28 +0800
comments: true
categories: [rails]
---

今天被問了一個問題當場答不出來，記錄起來，回家後試著實作看看

建立滿足以下關係的Table Associations

1. User can create a tag for a book.
2. According to a given tag, return book list.
3. According to a given book, return tag list.

提示：需要用到2個Join Table

以下作法不確定是否正確，閱讀時請斟酌

需要建立5個Table

```ruby Users
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name
      t.timestamp
    end
  end
end
```

```ruby Books
class CreateBooks < ActiveRecord::Migration
  def change
    create_table :books do |t|
      t.string :title
      t.timestamps
    end
  end
end
```

```ruby Tags
class CreateTags < ActiveRecord::Migration
  def change
    create_table :tags do |t|
      t.text :label
      t.timestamps
    end
  end
end
```

```ruby UserTags
class CreateUserTags < ActiveRecord::Migration
  def change
    create_table :user_tags do |t|
      t.integer :user_id
      t.integer :tag_id
    end
  end
end
```

```ruby BookTags
class CreateBookTags < ActiveRecord::Migration
  def change
    create_table :book_tags do |t|
      t.integer :book_id
      t.integer :tag_id
    end
  end
end
```

接著是Model設定

```ruby User
class User < ActiveRecord::Base
  has_many :user_tags
  has_many :tags, through: :user_tags
end
```

```ruby Book
class Book < ActiveRecord::Base
  has_many :book_tags
  has_many :tags, through: :book_tags
end
```

```ruby Tag
class Tag < ActiveRecord::Base
  has_many :user_tags
  has_many :users, through: :user_tags

  has_many :book_tags
  has_many :books, through: :book_tags
end
```

```ruby UserTag
class UserTag < ActiveRecord::Base
  belongs_to :user
  belongs_to :tag
end
```

```ruby BookTag
class BookTag < ActiveRecord::Base
  belongs_to :book
  belongs_to :tag
end
```

然後在Rails Console下測試

```ruby
# Create data
> User.create(name: "Tomo")
> Book.create(title: "Ruby")
> Tag.create(label: "good book")
> User.first.tags << Tag.first
> Book.first.tags << Tag.first

# Test associations
> User.first.tags # return tags belong to user
> Book.first.tags # return tags belong to book
> Tag.first.users # return tag's users
> Tag.first.books # return tag's books
```

看起來都有正常連結，這個作法的缺點應該是在user_tags和book_tags都有記錄tag_id這個橍位，在空間上的利用效率較差。不過目前我也沒想到其它的方法就是了。

