---
layout: post
title: "Translations in Drupal7"
date: 2014-02-04 03:35:11 -0600
comments: true
categories: Drupal
---

Trials and tribulations of a Drupal 7 multilingual site...
This is way less complicated if you set up your languages from the start.

Assumptions:

You have a working Drupal 7 site. (I'm using 7.22)
You know how to install modules.
Some drush experience is good but not necessary.
You need a second language on your site.
I spent some time investigating the best options for creating a multilingual site and this is what I've come up with. This works for me and may not work for everyone. I will list the modules and versions that I'm using in hopes that it will help someone else.

There may be a few modules that you will need depending on your use case. For this post I'm only going to touch on the translation/multilingual modules that you need.



NOTE: I ran into this problem and thought I'd bring it up now as it caused some issues and quite some time trying to find a solution.

If you have existing nodes more than likely they have a language of 'und ' in the database.
Please make sure you have a database backup before trying this. This is always good practice, especially if this is a production site. I recommend never running anything on production site or it's database before first trying it out in a development environment first.
Run this query in an update hook or (because I have a dev site) I just ran this query in SequelPro.
UPDATE node SET language = 'en' WHERE language = 'und';
^ This assumes you are using English as you default language.
​If at any time something doesn't seem to be showing up or displaying properly just clear the cache (drush cc all). I'm running Nginx, php5-fpm, and Varnish so I've had to do this a few times. This will just depend on your setup.

To get started you will need to download the following:

https://drupal.org/project/i18n
https://drupal.org/project/l10n_client
https://drupal.org/project/l10n_update
https://drupal.org/project/tmgmt
https://drupal.org/project/tmgmt_microsoft (for translations)
https://drupal.org/project/lang_dropdown
https://drupal.org/project/languageicons (adds icons to the switcher)
https://drupal.org/project/title
https://drupal.org/project/variable
Modules that we will enable:

Core
Locale (locale)
Contrib
Entity API (entity)
Views (views)
Ctools (ctools)
Views Bulk Operations (views_bulk_operations)
Internationalization (i18n)
Entity Translation (entity_translation)

Localization client (l10n_client)
Localization update (l10n_update)

Translation Management Core (tmgmt)
Translation Management UI (tmgmt_ui)
Entity Source (tmgmt_entity)
Entity Source User Interface (tmgmt_entity_ui)
Translation Management Field (tmgmt_field)
Microsoft Translator (tmgmt_microsoft)

Title (title)
Variable (variable)
Variable Admin (variable_admin)
Drush command to install all modules we need:

For this example I'm going to add Spanish as a second language. I'm also not going to use core's Content translation module but rather going to do this using Entity translation instead. This will allow us to have one node with multiple translations per field vs. having to have multiple nodes, one for each translation.

Add another language:

/admin/config/regional/language
Click + Add language
Choose <code>Spanish</code> from the select list
Click on the Detection and Selection tab
(fill this in)
From here you can go to: /admin/config/regional/translate and see how much has already been translated for you.

Entity translation:

/admin/config/regional/entity_translation
For now we will leave the defaults selected but we will be coming back here to add Taxonomy term and Comment to the Translatable Entity Types.
You should see that Node is already selected, if not then select it now and save.
i18n:

/admin/config/regional/i18n
Select Enabled languages only
Tmgmt:

/admin/config/regional/tmgmt
You can see that there haven't been any jobs created yet.
You may also see a warning at the top of the page:
Entity translation is not enabled for any of existing content types. To use this functionality go to Content types administration and enable entity translation for desired content types.
We will get to this in a moment...

Click on the Entity Sources tab
We haven't added any entities yet
Click on the Translators tab
We will be adding a translator as well.
On the Settings tab we will be leaving the defaults.
For all of your existing content types or any new ones:

/admin/structure/types
Click Edit on the content type you want to add Multilingual support for
Click on Publishing options and under Multilingual support select:
Enabled, with field translation
I also choose Hide content translation links because I will be using the language switcher block.
Save


Now head back over to: /admin/config/regional/tmgmt/entity

You should see a list of nodes on your system that belong to the content type you just edited.


If you would like to use the Microsoft translator then follow these steps:

Enable the Microsoft Translator (tmgmt_microsoft)
Now when you visit /admin/config/regional/tmgmt/translators you should see it.
We need to configure the translator
Click Edit
You will need to head over to: https://datamarket.azure.com/dataset/1899a118-d202-492c-aa16-ba21c33c06cb and sign up for a free account.
Under MICROSOFT TRANSLATOR PLUGIN SETTINGS​
Enter your Microsoft Customer Id and Primary Account Key

Just to make sure double check the REMOTE LANGUAGES MAPPINGS and make sure they are what you expect.

Save

There are a couple more things we need to do to make sure we make all of our fields available to the translator:

Go back to the content type that we are adding language options to.
Under the Manage Fields tab you should see an operation under 'Title' that allows you to replace it. Do this.
Now edit the 'Body'
At the bottom of the page under Field translation click Enable translation
Leave the migration setting checked
I cleared cache here (drush cc all)
Go back to the Entity Sources tab

Lets try a translation... Select one of your nodes and then click Request translation

You should notice that it wants to translate from English to Spanish and it should give you a word count.

Click Send to translator

When it's done you will be directed back to the Entity Sources tab. Click the yellow Ready for review box next to your node. If it looks good (or like me, you think it looks good) then click the checkmarks to the right of each field and Save as completed. You should now notice that the yellow box is now green.

Add the Language switcher block to a region and then test it out and make sure you can switch back and forth and see the expected results.

Remember, you will only see Spanish for the nodes that you have already translated.

If you end up with this error: Rejected by Microsoft Translator: TranslateApiExceptionMethod: Translate()Message: Cannot find an active Azure Market Place Translator Subscription associated with the request credentials.message id=3817.V2_Rest.Translate.3361868A

Do this:

https://datamarket.azure.com/developer/applications/register and register your application. Use the clientid and secret you created when you created your account.
