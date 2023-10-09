WorkingForumBundle
==================

Setup for Symfony 2/3/4 (without Flex structure)
------------------

This bundle use KnpPaginatorBundle for pagination, KnpMarkdown for markdown rendering and the extra package for Symfony

1/ Run  :
````bash
composer require "yosimitso/workingforumbundle":"^2.0"
````


2/ Register the bundle in your AppKernel
````php
  new Knp\Bundle\PaginatorBundle\KnpPaginatorBundle(),
  new Yosimitso\WorkingForumBundle\YosimitsoWorkingForumBundle(),
  new Knp\Bundle\MarkdownBundle\KnpMarkdownBundle(),
  new Sensio\Bundle\FrameworkExtraBundle\SensioFrameworkExtraBundle()
````

3/ Add the bundle's configuration into your app/config.yml

````yml
yosimitso_working_forum:
    thread_per_page: 50
    post_per_page: 10
    date_format: 'Y/m/d H:i:s'
    allow_anonymous_read: false             # ALLOW OR NOT ACCESS TO ANONYMOUS USERS
    allow_moderator_delete_thread: false
    theme_color: green                      # GREEN OR DARK_BLUE
    lock_thread_older_than: 0               # DAYS BETWEEN THE LAST THREAD'S POST AND THE AUTOLOCKING OF THE THREAD, 0 MEANS DISABLED
    post_flood_sec: 30                      # SECONDS MINIMUM BETWEEN EACH POST FROM A SAME USER 
    site_title: "My website"
    vote:
        threshold_useful_post: 5            # NUMBER OF VOTE NEEDED FOR A POST TO BE CONSIDERED AS USEFUL
    file_upload:
        enable: true                        # ALLOW OR NOT USERS TO UPLOAD ENCLOSED FILES 
        max_size_ko: 10000
        accepted_format: [image/jpg, image/jpeg, image/png, image/gif, image/tiff, application/pdf]
        preview_file: true                  # FOR IMAGES ONLY, DISPLAY A THUMBNAIL
    thread_subscription:                    # ALLOW OR NOT THREAD SUBSCRIPTION
        enable: true  
knp_paginator:
    page_range: 1                      # default page range used in pagination control
    default_options:
        page_name: page                # page query parameter name
        sort_field_name: sort          # sort field query parameter name
        sort_direction_name: direction # sort direction query parameter name
        distinct: true                 # ensure distinct results, useful when ORM queries are using GROUP BY statements
    template:
        pagination: "@YosimitsoWorkingForum/Common/slidePagination.html.twig"     # sliding pagination controls template
        sortable: "@KnpPaginator/Pagination/sortable_link.html.twig" # sort link template
````
If you decide to enable the file upload system, create a directory called "wf_uploads" into your web directory with writing rights,
please also check if your PHP configuration allow file upload through forms and adjust the directives "upload_max_filesize" and "post_max_size" to your application's config

4/ Add to you app/config.yml into 'orm' key :
````yml
resolve_target_entities:
    Yosimitso\WorkingForumBundle\Entity\User: You\YourUserBundle\Entity\YourUser
````

5/ Your User Entity needs to extends : \Yosimitso\WorkingForumBundle\Entity\User
Example :
````php
   class User extends \Yosimitso\WorkingForumBundle\Entity\User
{
    // YOUR ENTITY
}
````
In case your user entity already extends an another bundle (like FOSUserBundle), implement the interface \Yosimitso\WorkingForumBundle\Entity\UserInterface
in your user entity. Then copy/paste the content of \Yosimitso\WorkingForumBundle\Entity\User (attributes, getter, setter) into your user entity

6/ To import the bundle's routing, add to your app/routing.yml (you are free to modifiy the prefix) :
````yml
yosimitso_working_forum:
    resource: "@YosimitsoWorkingForumBundle/Resources/config/routing.yml"
    prefix:   /forum
````    

7/ Install the assets
````bash
php bin/console assets:install
````

8/ Update the database schema, check the SQL query generated by Doctrine :
````bash
php bin/console doctrine:schema:update --dump-sql
````
Then if the SQL query looks OK, run :
````bash
php bin/console doctrine:schema:update --force
````

9/ (Optionnal but necessary in many cases)
Override templates "Common/base.html.twig" and "Common/header.html.twig" to adapt the bundle templates to your application
Example : create the file "templates/bundles/YosimitsoWorkingForumBundle/Common/base.html.twig" with at least :
````twig
{% block forum %}
{% endblock %}
````
You can also override the translations files