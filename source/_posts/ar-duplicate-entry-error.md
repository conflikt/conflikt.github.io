---
title: Rails ActiveRecord – has_and_belongs_to_many & Duplicate entry Error
date: 2010-02-05
tags:
  - activerecord
  - rails
  - ruby
published: true
---

### Scenario
In One of my project, we had Many to Many association between models. This allowed me to look more deeply into the has_and_belongs_to_many(HABTM) association.

When we say:

```ruby
  class Movies < ActiveRecord::Base
    has_and_belongs_to_many :gallery_photos, :join_table=> "movies_gallery_photos"
  end

  class GalleryPhoto < ActiveRecord::Base
    has_and_belongs_to_many :movies, :join_table=> "movies_gallery_photos"
  end
```

Rails will assume there is a table “movies_gallery_photos” that include foreign keys to the two entities. So this table should be created by the following migration

```ruby
  create_table :movies_gallery_photos, :id=> false do |t|
    t.integer :movie_id, :null=> false
    t.integer :gallery_photo_id, :null=> false
  end
```

`:id=>false` will prevent the creation of the default primary key for that table.

As API documentation say: other attributes in that relation will be loaded with the objects and will be read only, including the :id. So failing to disable the id generation for that table will cause the loaded objects to have and “id” attribute holding the value of the id of the movies_gallery_photos entries instead of the ids of the target entity (movies or gallery_photos here).

#### Issue
Having (:id) false helps to over Mysql::Error: Duplicate entry ‘#’ for key #” entry error we faces while adding entries. `movie.gallery_photos << photo`. But it leads to creation and availability of duplicate values in the movies_gallery_photos table. i.e.

```ruby
  movies.gallery_photos << photos
```

will not check for uniqueness of the photos objects.
