# Minify Zend-Helper
---

This is a Zend Helper which integrates [minify](https://github.com/mrclay/minify) into your Zend project.

This is a fork off [bubba-h57 helper](https://github.com/bubba-h57/zf-helpers) and uses the [minify library](https://github.com/mrclay/minify).

## Prerequisites
* This file expects that you have installed minify in ../ZendFramworkProject/public/min and that it is working. 
* If your location has changed, modify $this->$_minifyLocation to your current location.


## Installation

* Simply drop this file into your ../ZendFramworkProject/application/views/helpers directory.
 
## Usage

### For CSS
* In your Layout or View scripts, you can simply call minifyHeadLink in the same way that you used to call headLink. Here is an example:

`
  <?php
  echo $this->minifyHeadLink('/favicon.ico')             // Whatever was already loaded from Controller.
  ->prependStylesheet('http://example.com/js/sample.css')// 6th
  ->prependStylesheet('/js/jqModal.css')                 // 5th
  ->prependStylesheet('/js/jquery.alerts.css')           // 4th
  ->prependStylesheet('/templates/main.css')             // 3rd
  ->prependStylesheet('/css/project.css.php')            // 2nd
  ->prependStylesheet('/css/jquery.autocomplete.css')    // 1st
  ->appendStylesheet('/css/ie6.css','screen','lt IE 7'); // APPEND to make it Last
  ?>
`

 * In the example above, the 2nd is a php file, and wehave a reference to a favicon link in there as well as a reference to a css file on another website. Because minify can't do anything with that php file (runtime configured css file) nor with CSS on other websites, and order is important,you would notice that the output in your browser will looks something like:

` 
  <link href="/min/?f=/css/jquery.autocomplete.css" media="screen" rel="stylesheet" type="text/css" />
  <link href="/css/project.css.php" media="screen" rel="stylesheet" type="text/css" />
  <link href="/min/?f=/templates/main.css,/js/jquery.alerts.css,/js/jqModal.css" media="screen" rel="stylesheet" type="text/css" />
  <link href="http://example.com/js/sample.css" media="screen" rel="stylesheet" type="text/css" />
  <link href="/favicon.ico" rel="shortcut icon" />
  <!--[if lt IE 7]> <link href="/css/ie6.css" media="screen" rel="stylesheet" type="text/css" /><![endif]-->
`

### For JavaScript

* In your Layout or View scripts, you can simply call minifyHeadScript in the same way that you used to call headScript. Here is an example:

` 
  echo $this->minifyHeadScript()
  ->prependFile('http://ajax.googleapis.com/ajax/libs/someObject/2.2/object.js') // 12th
  ->prependFile('/js/jquery.delaytrigger.js') // 11th
  ->prependFile('/js/sorttable.js')           // 10th
  ->prependFile('/js/jquery.alerts.js')       // 9th
  ->prependFile('/js/jqModal.js')             // 8th
  ->prependFile('/js/jquery.maskedinput.js')  // 7th
  ->prependFile('/js/jquery.checkbox.js')     // 6th
  ->prependFile('/js/jquery.tablesorter.min.js') // 5th
  ->prependFile('/js/jquery.autocomplete.js') // 4th
  ->prependFile('/js/jquery.color.js')        // 3rd
  ->prependFile('/js/jquery-1.3.2.min.js')    // 2nd
  ->prependFile('/js/main.js')                // 1st
  ->appendScript('$(document).ready(function() {
		$(\'#ajaxWait\').ajaxStart(function() {
	      $(this).show();
	    }).ajaxStop(function() {
	      $(this).hide();
		      });
		try { init(); } catch(e) {}
	});                                       // Last
 ');
` 
 
* Because minify can't do anything with a javascript from some other server, nor does it do anything with inline scripts, and order is important, it will minify up to the point that it meets something that can't be minified, and then output the minified version, then the item(s) that couldn't be minified, and then attempt to minify items again, repeating the process till it is completed. Here is an example of output from the example above.

` 
  <script type="text/javascript" src="/min/?f=/js/main.js,/js/jquery-1.3.2.min.js,/js/jquery.color.js,/js/jquery.autocomplete.js,/js/jquery.tablesorter.min.js,/js/jquery.checkbox.js,/js/jquery.maskedinput.js,/js/jqModal.js,/js/jquery.alerts.js,/js/sorttable.js,/js/jquery.delaytrigger.js"></script>
  <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/someObject/2.2/object.js"></script>
  <script type="text/javascript">
  //<![CDATA[
      $(document).ready(function() {
	      $('#ajaxWait').ajaxStart(function() {
		      $(this).show();
		  }).ajaxStop(function() {
		      $(this).hide();
		  });
			try { init(); } catch(e) {}
	   });
  //]]>
  </script>
`
 
 * Sometimes you need the ability to control how script files are combined together, or need several javascript files that should not be compressed. 

`
  $view->headScript()
  ->appendFile('js/js1.js')
  ->appendFile('js/js2.js')
  ->appendFile('js/js3.js', 'text/javascript', array('minify_split_after' => true));
  ->appendFile('js/js4js')
`

* These files will be transformed to:

`
  <script text="javascript" src="/min/?f=js/js1.js,js/js2.js,js/js3.js">
  <script text="javascript" src="/min/?f=js/js4.js">
`

* It also supports "minify_split_before" and "minify_disabled" that turns compression off for specific script.

`
  $view->headScript()
  [->appendFile('js/js1.js', 'text/javascript', array('minify_disabled' => true))
   ->appendFile('js/js2.js')
   ->appendFile('js/js3.js', 'text/javascript', array('minify_split_before' => true));
   ->appendFile('js/js4js')]]
`

`
  <script type="text/javascript" src="/js/js1.js"></script>
  <script text="javascript" src="/min/?f=js/js2.js">
  <script text="javascript" src="/min/?f=js/js3.js,js/js4.js">
`

* Use this trick to enable/disable minify depending on the config file

`
  if ($config->minifyJavascriptAndCSS) {
      $view->registerHelper(new Zend_View_Helper_MinifyHeadScript(), 'headScript');
      $view->registerHelper(new Zend_View_Helper_MinifyHeadLink(), 'headLink');
  }
`     

