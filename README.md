Elao blog data
===========

Blog posts and structure of the  [http://www.elao.com/blog](Elao's Blog) 


##Users file:
Must be at the root of the repo and named _users.json_


_Example of users.json file:_

    [{
        "name":  "Vincent",
        "slug":  "vincent",
        "email": "vincent.bouzeran@elao.com"
    },{
        "name":  "Guewen",
        "slug":  "guewen",
        "email": "guewen.faivre@elao.com"
    }]


##Tags file:
Must be at the root of the repo and be named _tags.json_
It contains the allowed tags used in posts. If a post use a tag not referenced in these file, the tag will be ignored.

_Example of tags.json file:_

    [
        "Symfony2",
        "HTML/CSS",
        "Webdesign",
        "Framework",
        "Osef"
    ]



##Posts file:
The post files must be placed in the /posts folder. Each post must have two file: a markwon

For each post, you need to have 2 files in the /posts folder

 * The **.md** file that should contains the markdown content of the post
 * The **.meta** file that should contains the meta data for the article.

The two files must have the same name except the extension.

_Example:_

If your post is named "mysuperblogpost", you must have these two files in the /posts folder:

* **mysuperblogpost.md**     (hold the markdown content of the post)
* **mysuperblogpost.meta**   (hold the metas of the post)

##Post meta file
The post meta file contains information about the associated blog post.

The metas are like this:
<table>
	<tr><th>Meta</th><th>Description</th></tr>
	<tr><td>_slug_ \*</td><td>The post's slug (used to identify the post. Must be unique among all posts)</td></tr>
	<tr><td>_title_ \*</td><td>The post's title</td></tr>
	<tr><td>_tags_</td><td>List of post's tag (must be reference by name of tags contains in the tags's file)</td></tr>
	<tr><td>_status_ \*</td><td>The post's status ("published", "draft")</td></tr>
	<tr><td>_publish_by_ \*</td><td>The publisher of the post (must be the reference of a user contains in the users's file either by email or slug)</td></tr>
	<tr><td>_publish_at_ \*</td><td>A date (any format supported by Moment.js)</td></tr>	
	<tr><td>_meta_title_</td><td>The html meta title of the post</td></tr>
	<tr><td>_meta_description_</td><td>The html meta description of the post</td></tr>
</table>
<i><small>* indicate required metas</small></i>


_Example of a post meta file:_

    {
        "tags":	           		["Symfony2", "Toto", "Titi"],
        "title":				"Imported post in ghost",
        "slug":					"imported-post-in-ghost",
        "status":				"published",
        "meta_title":			"Imported post in ghost",
        "meta_description":		"An imported post into ghost description",
        "publish_by":"			vincent.bouzeran@elao.com",
        "publish_at":			"2014-07-06"
    }

