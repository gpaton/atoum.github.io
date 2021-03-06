---
layout: static
title: Features
---

# Features

<p class="header__paragraph">
    atoum is a full-featured testing framework. Judge for yourself.
</p>

<section class="visual-section">
## Flexible structure

Test suites, test cases, directory names… feel free to rename everything to fit your particular needs.
</section>

<section class="visual-section">
## Execution engines

By default, atoum provides three [execution engines][execution-engine]:

<ul class="bare block-list">
    <li style="max-width: 25%">
        <img src="/images/icon/inline.svg" style="width: 100px; height: 100px; display: block; margin: 1rem auto"/>
        <strong>Inline</strong>, one test case after another in the same
        process,
    </li>
    <li style="max-width: 25%">
        <img src="/images/icon/isolate.svg" style="width: 100px; height: 100px; display: block; margin: 1rem auto"/>
        <strong>Isolate</strong>, one test case after another but each time in a
        new process,
    </li>
    <li style="max-width: 25%">
        <img src="/images/icon/concurrent.svg" style="width: 100px; height: 100px; display: block; margin: 1rem auto"/>
        <strong>Concurrent</strong>, “all” test cases at the same time in
        separated processes.
    </li>
</ul>

A specific engine can be defined per test case in addition to a default one with the <code>&commat;engine</code> annotation.

Using the concurrent engine will provide a faster feedback, this will accelerate your development.
</section>

<section class="visual-section">
## Vocabulary

Depending of the kind of tests we are making, there is several ways to write a test case. The “classic” (old) way is 
procedural:

{% highlight php %}
<?php

$x      = 1;
$y      = 2;
$result = $x + $y;

$this->assertTrue($result === 3);
{% endhighlight %}

The “smarter” way by really using atoum's asserters:

{% highlight php %}
<?php

$x      = 1;
$y      = 2;
$result = $x + $y;

$this->integer($result)->isEqualTo(3);
{% endhighlight %}

The “academic” way:

{% highlight php %}
<?php

$this
    ->given(
        $x = 1,
        $y = 2
    )
    ->when($result = $x + $y)
    ->then
        ->integer($result)
            ->isEqualTo(3);
{% endhighlight %}

In the later example, the `given`, `when` and `then` keywords are “empty asserters”. They execute nothing. They are used 
to provide a better test case design.
</section>

<section class="visual-section">
## Meaningful asserters

atoum provides a [full-featured set of natural and expressive assertions][asserters] making test cases as much readable 
as possible. The following example asserts the integer `150` is greater than `100` and lower than or equal to `200`:

{% highlight php %}
<?php

$this
    ->integer(150)
        ->isGreaterThan(100)
        ->isLowerThanOrEqualTo(200);
{% endhighlight %}

The following example asserts that `1 - 0.97` is nearly equal to `0.03` (which is
[strictly false in Computer Science][float-comparison]):

{% highlight php %}
<?php

$this
    ->float(1 - 0.97)
        ->isNearlyEqualTo(0.03) // passes
        ->isEqualTo(0.03);      // fails
{% endhighlight %}

Arrays, strings, objects, exceptions… all of them have specific collections of asserters. In addition to provide a
better readability, they provide a better <strong>feedback</strong> when a test fails because atoum knows what you meant
to do.

A “**diff**” between the expected value and the computed value is also produced when a test fails.
</section>

<section class="visual-section">
## Mocks

Mocks able to close dependencies of your system under test. Test cases will be faster to build and to execute. atoum
provides mocking for:

* **Classes**, by simply using the top-namespace `mock` (case insensitive); for instance, the following example will
  mock the `Foo\Bar` class and will provide a new implementation for the `methodBaz` method:

{% highlight php %}
<?php

$mockedObject = new \Mock\Foo\Bar();

$this->calling($mockedObject)->methodBaz = function ($x) {
    return $x * 2;
};
{% endhighlight %}

* **Functions**, by using the `function` “asserter”; the following example will mock the
  [`session_start`][session_start] PHP function and will provide a new implementation:

{% highlight php %}
<?php

$this->function->session_start = function () {
    return false;
};
{% endhighlight %}

* **Constants**, by using the `constant` “asserter”; the following example will mock the
  [`PHP_VERSION_ID`][phpversion] constant and will provide a new value:

{% highlight php %}
<?php

$this->constant->PHP_VERSION_ID = 606060; // troll spotted
{% endhighlight %}

Mocks are generated at runtime and they are just that easy to use.

Of course, you can mock class constructor, you can control the value to compute each time a method is called, you can 
assert that a method has been called at least once etc.
</section>

<section class="visual-section">
## Virtual file system

When manipulating files or directories, having real files is not required. atoum provides a virtual file system allowing
to fake real files and directories. Enter `atoum://`. The following example will create a virtual file and the resulting
resource can be used like any other regular file resources:

{% highlight php %}
<?php

$file = atoum\mock\streams\fs\file::get('fakeFile');
fwrite($file, 'foobar');
rewind($file);
// …
stream_get_contents($file); // string(6) "foobar"
{% endhighlight %}

As expected, you can control the permissions, the ownership, different times, content, parents etc.
</section>

<section class="visual-section">
## Reports

Either test suites are run by one single user, or by a continous integration server. In both cases (but most importantly
in the latter), having test reports is crucial to understand failures, regressions, performances etc. That's why atoum
is able to produce several reports like:

* [TAP][tap], the Test Anything Protocol, a language agnostic format,
* [clover], a software and a
  format for test reports,
* [xUnit][xunit], a Jenkins plugin and a format for test
  reports,
* Your own, yes, this is really easy to plug your own reporter.

Whatever you are using [Jenkins][jenkins], [Travis][travis] or any
other well-known softwares, they are likely to understand atoum.
</section>

<section class="visual-section">
## Loop and autorun modes

In order to help you as much as possible, atoum provides the **loop mode**. Simply using the `--loop` option on the
command-line tool will run all the tests and it will wait you to press <kbd>Enter</kbd> before restarting the run. If
test cases were failling, atoum will **only re-run these ones**. You will save time and CPU. If no test cases were
failling, atoum will re-run all your test suites.

{% highlight sh %}
$ bin/atoum … --loop
…
Press <Enter> to reexecute, press any other key and <Enter> to stop...
{% endhighlight %}

The autorun helps you to run any test cases in any file like a regular PHP file if the runner is included. It means you
can avoid using the command-line tool. Thus, both following examples are equivalent (assuming the runner is included):

{% highlight sh %}
$ bin/atoum --files Test/Unit/Foo.php
$ # is equivalent to…
$ bin/atoum Test/Unit/Foo.php
$ # is equivalent to…
$ php Test/Unit/Foo.php
{% endhighlight %}
</section>

<section class="visual-section">
## Configuration file in PHP

atoum's configuration file is written in PHP. No YAML, no XML, no strange language. Only PHP for a maximum flexibility.

{% highlight php %}
<?php

$script->addDefaultArguments(
    '--debug',
    '--use-tap-report',
    '--loop'
);
$runner->addTestsFromDirectory(__DIR__ . '/Test/Unit/');
// …
{% endhighlight %}

Note the global `$script` and `$runner` variables: reach any part of atoum to fit in your own workflow.
</section>

<section class="visual-section">
## Third-party integration

atoum integrates well with those CI tools:
<ul class="columns integrations" data-columns="4">
    <li>
       <img src="images/integrations_logos/travis-ci.svg" />
       <a href="https://travis-ci.org/">Travis CI</a>
       <a href="http://docs.atoum.org/en/latest/cookbook.html#use-with-travis-ci" class="integration__doc-link">(see how)</a>
    </li>
    <li>
       <img src="images/integrations_logos/jenkins.svg" />
       <a href="https://jenkins-ci.org/">Jenkins</a>
       <a href="http://docs.atoum.org/en/latest/cookbook.html#use-inside-jenkins-or-hudson" class="integration__doc-link">(see how)</a>
    </li>
    <li>
       <img src="images/integrations_logos/gitlab.svg" />
       <a href="https://about.gitlab.com/gitlab-ci/">GitLab CI</a>
       <a href="http://doc.gitlab.com/ce/ci/languages/php.html#using-atoum" class="integration__doc-link">(see how)</a>
    </li>
    <li>
       <img src="images/integrations_logos/circleci.svg" />
       <a href="https://circleci.com/">Circle CI</a>
    </li>
</ul>

atoum has a **great** integration with those IDEs:
<ul class="columns integrations" data-columns="4">
    <li>
      <img src="images/integrations_logos/phpstorm.svg" />
      <a href="https://www.jetbrains.com/phpstorm/">PhpStorm</a>
      <a href="https://github.com/atoum/phpstorm-plugin" class="integration__doc-link">(see how)</a>
    </li>
    <li>
      <img src="images/integrations_logos/atom.svg" />
      <a href="https://atom.io/">Atom</a>
      <a href="https://github.com/atoum/atom-plugin" class="integration__doc-link">(see how)</a>
    </li>
    <li>
       <img src="images/integrations_logos/sublime-text.svg" />
       <a href="http://www.sublimetext.com/">Sublime Text</a>
       <a href="http://docs.atoum.org/en/latest/ide.html#sublime-text-2" class="integration__doc-link">(see how)</a>
    </li>
    <li>
       <img src="images/integrations_logos/netbeans.svg" />
       <a href="http://www.sublimetext.com/">NetBeans</a>
       <a href="http://docs.atoum.org/en/latest/ide.html#sublime-text-2" class="integration__doc-link">(see how)</a>
    </li>
</ul>

but atoum also plays well with:
<ul>
    <li><a href="http://www.vim.org/">Vim</a> <a href="http://docs.atoum.org/en/latest/ide.html#vim" class="integration__doc-link">(see how)</a></li>
    <li><a href="https://github.com/macvim-dev/macvim">macvim</a> <a href="http://docs.atoum.org/en/latest/ide.html#macvim" class="integration__doc-link">(see how)</a></li>
    <li><a href="http://portablegvim.sourceforge.net/">GVim</a> <a href="http://docs.atoum.org/en/latest/ide.html#gvim" class="integration__doc-link">(see how)</a></li>
    <li><a href="https://wiki.gnome.org/Apps/Gedit">gedit</a> <a href="http://docs.atoum.org/en/latest/ide.html#gedit" class="integration__doc-link">(see how)</a></li>
</ul>

You can also use atoum with your regular build tools and/or dependancy manager:

<ul class="columns integrations" data-columns="3">
    <li>
       <img src="images/integrations_logos/composer.svg" />
       <a href="https://getcomposer.org/">Composer</a>
       <a href="http://docs.atoum.org/en/latest/getting_started.html#composer" class="integration__doc-link">(see how)</a>
    </li>
    <li>
       <img src="images/integrations_logos/phing.svg" />
       <a href="http://www.phing.info/">Phing</a>
       <a href="http://docs.atoum.org/en/latest/cookbook.html#step-3-launching-tests-via-jenkins-or-hudson" class="integration__doc-link">(see how)</a>
    </li>
    <li>
       <img src="images/integrations_logos/robo.svg" />
       <a href="http://robo.li/">Robo</a>
       <a href="http://robo.li/tasks/Testing/#atoum" class="integration__doc-link">(see how)</a>
    </li>
</ul>

</section>

<section class="visual-section">
## Fast

First, atoum comes with a concurrent engine, which makes it fast (and secure) by default. Second, atoum is lightweight.
For instance, the following table shows the time and memory required to run atoum's test suite and [Hoa][hoa]'s test 
suite, which are known to be important and intensive, both in terms of computation and memory:

<table style="max-width: 500px">
    <caption>This table illustrates how fast atoum is by showing numbers about
        big test suites.</caption>
    <thead>
    <tr>
        <th></th>
        <th>atoum</th>
        <th>Hoa</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>Test suites</td>
        <td>224</td>
        <td>81</td>
    </tr>
    <tr>
        <td>Test cases</td>
        <td>1816</td>
        <td>611</td>
    </tr>
    <tr>
        <td>Assertions</td>
        <td>26,774</td>
        <td>177,976</td>
    </tr>
    <tr class="table--double-separator">
        <td>Time</td>
        <td>25s</td>
        <td>93s</td>
    </tr>
    <tr>
        <td>Memory</td>
        <td>58Mb</td>
        <td>50Mb</td>
    </tr>
    </tbody>
</table>
</section>

<section class="visual-section">
## Extensions

atoum is extensible. The community writes extensions and the organization of atoum hosts and maintains some of them,
like:

* [`atoum/bdd-extension`][bdd-extension], to write BDD-like test cases,
* [`atoum/reports-extension`][reports-extension], to write code coverage reports (showing branch and path coverage on 
  the code or on the control flow graph),
* [`atoum/ruler-extension`][ruler-extension], to precisely filter test cases to run with a “natural language”,
* [`atoum/json-schema-extension`][json-schema-extension], to validate JSON documents with new asserters,
* [`atoum/visibility-extension`][visibility-extension], to bypass the visibility of methods,
* [`atoum/blackfire-extension`][blackfire-extension], to write your Blackfire test suites with atoum,
* [`atoum/deprecated-extension`][deprecated-extension], to get a report on deprecation notices.

Most of the time, to install an extension you will just need to include it in your dependencies and add one line in your
configuration file, like:

{% highlight php %}
<?php

$extension = new mageekguy\atoum\ruler\extension()
$extension->addToRunner($runner);
{% endhighlight %}
</section>

[execution-engine]: http://docs.atoum.org/en/latest/written_help.html#execution-engine
[asserters]: http://docs.atoum.org/en/latest/asserters.html
[float-comparison]: http://www.floating-point-gui.de/errors/comparison/
[session_start]: http://php.net/session_start
[phpversion]: http://php.net/phpversion
[tap]: http://testanything.org/
[clover]: https://confluence.atlassian.com/display/CLOVER/Clover+Documentation+Home
[xunit]: https://wiki.jenkins-ci.org/display/JENKINS/xUnit+Plugin
[jenkins]: https://jenkins-ci.org/
[travis]: https://travis-ci.org/
[hoa]: http://hoa-project.net/
[atom]: https://atom.io/
[circleci]: https://circleci.com/
[composer]: https://getcomposer.org/
[gvim]: http://portablegvim.sourceforge.net/
[phpstorm]: https://www.jetbrains.com/phpstorm/
[phing]: http://www.phing.info/
[vim]: http://www.vim.org/
[scrutinizer]: https://scrutinizer-ci.com/
[sublimetext]: http://www.sublimetext.com/
[gedit]: https://wiki.gnome.org/Apps/Gedit
[macvim]: https://github.com/macvim-dev/macvim
[bdd-extension]: /atoum/#/bdd-extension
[reports-extension]: /atoum/#/reports-extension
[ruler-extension]: /atoum/#/ruler-extension
[json-schema-extension]: /atoum/#/json-schema-extension
[visibility-extension]: /atoum/#/visibility-extension
[blackfire-extension]: /atoum/#/blackfire-extension
[deprecated-extension]: /atoum/#/deprecated-extension
