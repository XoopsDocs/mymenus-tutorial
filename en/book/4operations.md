# 4.0 Operating Instructions

###Theory of operation

Mymenus separates logic from presentation almost 100%!

It is mymenus task to generate an array with menus information and let each skin decide what to do with that information.

How can skin know when to open a new ```<li>``` or ```<ul>``` and when to close it?

Mymenus append that information to each menu item, that way you can know if that item should be prefixed with a <li> or not.

This is the composition of an item array:
```
 [id] => 3 (id of the menu item)
 [pid] => 0 (id of the parent menu item)
 [mid] => 2 (id of the menu package)
 [title] => Home (title of the menu item)
 [alt_title] => Home (alt/title of the menu item)
 [visible] => 1 (visibility of the menu item, it will be 1 for all menus,you can disregard it)
 [link] => http://localhost/xoops-2.4.5/htdocs/ (alt/title of the menu item)
 [weight] => 1 (this is for internal usage of the builder class, you can disregard it)
 [target] => _self (to be used in link target element, it can be _self, _blank, etc..)
 [groups] => Array (holds the groups who can view this link, you can disregard it)
 ([0] => 2
  [1] => 3
 )
 [hooks] => Array (holds the hooks available to render the menu, you can disregard it)
 ()
 [image] => (image to be used in the link, you can choose not to support it in your skin)
 [css] => (this is inline css for this item, it goes inside <a style="$item.css">)
 [oul] => 1 (IMPORTANT! Open UL -> this menu item requires skin to prepend <ul> open element tag)
 [oli] => 1 (IMPORTANT! Open LI -> this menu item requires skin to prepend <li> open element tag)
 [close] => (IMPORTANT! this holds closing tags, it will automatically generate </li></ul> tags for you)
 [cul] => (IMPORTANT! Close UL -> this menu item requires skin to append </ul> close element tag, you should use [close] instead, unless you are not supporting multilevel menus)
 [cli] => (IMPORTANT! Close LI -> this menu item requires skin to append </li> close element tag, you should use [close] instead, unless you are not supporting multilevel menus)
 [hassub] => 1 (informs if this menu item has submenus, 1 for true, 0 for false)
 [level] => 0 (informs the level of nesting of the menu item, 0 is for root, 1 for second level, etc..)
 [down_weight] => 3 (for usage in menu sorting in admin side, you can ignore it)
 [selected] => 1 (IMPORTANT, this tells the skin to highlight this item)
 [topselected] => 1 (Important, this informs the skin that the menu is of level 0(root) and it is selected, you should highlight it)
 )
```

####Skin structure

Skins go into
```
"mymenus/skins" folder or

"public_html/themes/yourtheme/menu" folder
```
and they should have a skin_version.php file in it

**skin_version.php structure:**
```
 //informs where to find the template for this skin(relative to skin folder)

 $skinVersion['template'] = 'templates/template.tpl';

 //informs where to find css file/files

 $skinVersion['css'] = 'assets/css/superfish.css';
```
 or
```
 $skinVersion['css'] = array('css/superfish.css', css/anotherone.css);

 //informs where to find js file/files

 $skinVersion['js'] = '../../js/assets/jquery-1.3.2.min.js';
```
 or
```
 $skinVersion['js'] = array(

 '../../js/jquery-1.3.2.min.js'

 '../../js/assets/hoverIntent.js',

 '../../js/superfish.js'

 );
```

** //code to be appended in the <head> theme tag**
```
 $header = "\n" . '<script type="text/javascript">';
 $header .= "\n" . ' var $sf = jQuery.noConflict()';
 $header .= "\n" . ' $sf(function(){';
 $header .= "\n" . ' $sf(\'ul.sf-menu\').superfish({';
 $header .= "\n" . ' delay: 1000,';
 $header .= "\n" . ' animation:
 {opacity:\'show\',height:\'show\'},';
 $header .= "\n" . ' speed: \'fast\'';
 $header .= "\n" . ' });';
 $header .= "\n" . ' });';
 $header .= "\n" . '</script>';
 $skinVersion['header'] = $header;
```
** //you can pass any configuration from this file to the template using ['config']**

 example:
```
 $skinVersion['config']['home'] = true;
 $skinVersion['config']['iconset'] = 'default';
```
 This can be fetched in template with ```<{$config.home}>``` and ```<{$config.iconset}>```


#### Smarty variables available in the template
```
$block - holds an array of menu items
$config - holds configuration set in skin_version.php
$skinurl - holds the url of the skin
$skinpath - holds the path of the skin
```

#### For Theme designers

Since users can choose the smarty variable for each menu,

I would advise you to use <{$xoops_links_navbar}> as a place holder.

If you provide a skin for your theme, ask users to:
```
-- --enter "xoops_links_navbar" as unique_id in block settings.

-- --set "render to smarty variable" in block settings.

-- --set "use skin from theme" in block settings.
```


###Important to know:

Links and images are relative to the root of your site:

```
modules/profile
search.php
uploads/blank.gif
```

For linking to external sites you need to use complete url:

http://www.xuups.com

You can use DECORATORS for links, images, title, and alt_title.

The decorators follow this syntax:

```
{decorator|value}
```

**There are 6 decorators available:**

* USER -> gets info for the user that is seeing the page
* OWNER -> gets info for the user that match uid on the url(if given)
* URI -> gets info about the url arguments
* MODULE -> gets dynamic menu from a module (Used in title field only)
* SMARTY -> gets smarty variables
* CONSTANT -> gets defined constants

Some syntax examples{USER|UNAME} gets the username of this user, returns anonymous if not a user{USER|UID} gets the uid of this user, returns 0 if not a user{USER|REGDATE} gets the regdate of this user, returns empty if not a user{USER|any other field of the user table} yes! You can get what you need!Some special fields you may use:

```
{USER|PM_NEW} Show number of private messages not read
{USER|PM_READED}
{USER|PM_TOTAL}
```

The same is valid for OWNER:

```
{OWNER|UNAME}
{OWNER|UID}etc..
```

And you can get any parameter on the uri with:

```
{URI|UID}{URI|ID}
{URI|SEARCH}
{URI|ITEMID}
{URI|CATID}etc...
```

Example of links using decorators:

```
modules/profile/userinfo.php?uid={USER|UID}
modules/yogurt/pictures.php?uid={OWNER|UID}
```

Example on titles using decorators:

```
{USER|UNAME}
{OWNER|UNAME} profile
```

You have searched for {URI|SEARCH}Populating menus with modules information:

```
{MODULE|NEWS}
{MODULE|XHELP}
{MODULE|MYLINKS}
{MODULE|TDMDOWNLOADS}
```

Using smarty information:

```
{SMARTY|xoops_uname}
{SMARTY|xoops_avatar}
```

Using constants information:

```
{CONSTANT|XOOPS_URL}/myimages/image.gif
{CONSTANT|XOOPS_ROOT_PATH}
```


