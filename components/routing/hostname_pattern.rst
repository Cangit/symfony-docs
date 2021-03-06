.. index::
   single: Routing; Matching on Hostname

How to match a route based on the Hostname
==========================================

.. versionadded:: 2.2
    Hostname matching support was added in Symfony 2.2

You can also match on the HTTP *hostname* of the incoming request.

.. configuration-block::

    .. code-block:: yaml

        mobile_homepage:
            pattern:  /
            hostname_pattern: m.example.com
            defaults: { _controller: AcmeDemoBundle:Main:mobileHomepage }

        homepage:
            pattern:  /
            defaults: { _controller: AcmeDemoBundle:Main:homepage }

    .. code-block:: xml

        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://symfony.com/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://symfony.com/schema/routing
                http://symfony.com/schema/routing/routing-1.0.xsd"
        >

            <route id="mobile_homepage" pattern="/" hostname-pattern="m.example.com">
                <default key="_controller">AcmeDemoBundle:Main:mobileHomepage</default>
            </route>

            <route id="homepage" pattern="/">
                <default key="_controller">AcmeDemoBundle:Main:homepage</default>
            </route>
        </routes>

    .. code-block:: php

        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('mobile_homepage', new Route('/', array(
            '_controller' => 'AcmeDemoBundle:Main:mobileHomepage',
        ), array(), array(), 'm.example.com'));

        $collection->add('homepage', new Route('/', array(
            '_controller' => 'AcmeDemoBundle:Main:homepage',
        )));

        return $collection;

Both routes match the same pattern ``/``, however the first one will match
only if the hostname is ``m.example.com``.

Placeholders and Requirements in Hostname Patterns
--------------------------------------------------

If you're using the :doc:`DependencyInjection Component</components/dependency_injection/index>`
(or the full Symfony2 Framework), then you can use
:ref:`service container parameters<book-service-container-parameters>` as
variables anywhere in your routes.

You can avoid hardcoding the domain name by using a placeholder and a requirement.
The ``%domain%`` in requirements is replaced by the value of the ``domain``
dependency injection container parameter.

.. configuration-block::

    .. code-block:: yaml

        mobile_homepage:
            pattern:  /
            hostname_pattern: m.{domain}
            defaults: { _controller: AcmeDemoBundle:Main:mobileHomepage }
            requirements:
                domain: %domain%

        homepage:
            pattern:  /
            defaults: { _controller: AcmeDemoBundle:Main:homepage }

    .. code-block:: xml

        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://symfony.com/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://symfony.com/schema/routing http://symfony.com/schema/routing/routing-1.0.xsd">

            <route id="mobile_homepage" pattern="/" hostname-pattern="m.example.com">
                <default key="_controller">AcmeDemoBundle:Main:mobileHomepage</default>
                <requirement key="domain">%domain%</requirement>
            </route>

            <route id="homepage" pattern="/">
                <default key="_controller">AcmeDemoBundle:Main:homepage</default>
            </route>
        </routes>

    .. code-block:: php

        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('mobile_homepage', new Route('/', array(
            '_controller' => 'AcmeDemoBundle:Main:mobileHomepage',
        ), array(
            'domain' => '%domain%',
        ), array(), 'm.{domain}'));

        $collection->add('homepage', new Route('/', array(
            '_controller' => 'AcmeDemoBundle:Main:homepage',
        )));

        return $collection;

.. _component-routing-hostname-imported:

Adding a Hostname Pattern to Imported Routes
--------------------------------------------

You can set a hostname pattern on imported routes:

.. configuration-block::

    .. code-block:: yaml

        # app/config/routing.yml
        acme_hello:
            resource: "@AcmeHelloBundle/Resources/config/routing.yml"
            hostname_pattern: "hello.example.com"

    .. code-block:: xml

        <!-- app/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://symfony.com/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://symfony.com/schema/routing http://symfony.com/schema/routing/routing-1.0.xsd">

            <import resource="@AcmeHelloBundle/Resources/config/routing.xml" hostname-pattern="hello.example.com" />
        </routes>

    .. code-block:: php

        // app/config/routing.php
        use Symfony\Component\Routing\RouteCollection;

        $collection = new RouteCollection();
        $collection->addCollection($loader->import("@AcmeHelloBundle/Resources/config/routing.php"), '', array(), array(), array(), 'hello.example.com');

        return $collection;

The hostname pattern ``hello.example.com`` will be set on each route
loaded from the new routing resource.
