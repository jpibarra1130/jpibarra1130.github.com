---
layout: post
title: "Java, Spock, and PowerMock"
description: "Making PowerMock work with Spock for mocking a Java Static method"
tags: [java, spock, powermock, test]
---

The thing I hate about Java is that at times, it's just so verbose. And if you're writing tests, it gets too time-consuming. Just the number of boilerplate you need makes it a little annoying to use.

I was playing around with [Spock](https://github.com/spockframework/spock) as a way to make test writing in Java much more fun. The problem is, the project I am currently working on is using a lot of static classes. So that's one big hurdle I had to face.

I couldn't find a lot of references about Spock natively being able to mock a static method for a Java class. But good thing is that we could use a Java Mocking Library. I decided to choose [PowerMock](https://code.google.com/p/powermock/) simply because the project is already using it.

It took me a while to make Spock work with PowerMock though. I think that there's not much references out there. There were a bunch of references saying that PowerMock couldn't be used together with Spock. Luckily, I managed to stumble upon this [repository](https://github.com/kriegaex/Spock_PowerMock) which allowed me to successfully use PowerMock with Spock.

To use PowerMock, you need to add the following dependencies. The PowerMock :

    <dependency>
      <groupId>org.powermock</groupId>
      <artifactId>powermock-module-junit4</artifactId>
      <version>${powermock.version}</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.powermock</groupId>
      <artifactId>powermock-module-junit4-rule</artifactId>
      <version>${powermock.version}</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.powermock</groupId>
      <artifactId>powermock-classloading-xstream</artifactId>
      <version>${powermock.version}</version>
      <scope>test</scope>
    </dependency>

As of writing, I used __1.5.4__ version of PowerMock.

My final test class looks something like this:

    import org.junit.Rule
    import org.mockito.Mockito
    import org.powermock.api.mockito.PowerMockito
    import org.powermock.core.classloader.annotations.PrepareForTest
    import org.powermock.modules.junit4.rule.PowerMockRule
    import spock.lang.Specification

    @PrepareForTest([StaticClass.class])
    public class FlightFormSpec extends Specification {

        @Rule PowerMockRule powerMockRule = new PowerMockRule();

        def "When mocking static"() {
            setup :
                PowerMockito.mockStatic(StaticClass.class)

            when :
                Mockito.when(StaticClass.getStaticMethod()).thenReturn("Philippines!");

            then :
                StaticClass.getStaticMethod() == "Philippines!"
        }
    }

I hope that this article does help other people who were stuck at trying to mock static methods using Spock.