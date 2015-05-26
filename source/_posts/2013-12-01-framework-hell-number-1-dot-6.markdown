---
layout: post
title: "framework hell #1.6"
date: 2013-12-01 21:29:00 +0200
comments: true
categories:
- cucumber
- IT tests
- java
- jbehave
- junit
- tutorial
- unit tests
---
As I promised this post will show basic, maybe not simplest but definitely most effective setup of both Cucumber and JBehave, which you can in most cases copy-paste into your project.

<!-- more -->

Project structure was published in last post.
Now for class implementation.

Cucumber first.
This is the simplest ever setup. Of course it won't be the simplest one, required to run Cucumber, but this one allows you to define which steps file can be used with desired feature file.

``` java
package cucumber.unit;

import cucumber.junit.Cucumber;
import org.junit.runner.RunWith;

@RunWith(Cucumber.class)
@Cucumber.Options(
        features={"src/test/resources/cucumber/unit/UnitTests.feature"},
        glue="cucumber.unit.steps")
public class CucumberUnitTest {
}
```

And now few words of explanation:

``` java
@RunWith(Cucumber.class)
```

tells JUnit which class should be used to run Cucumber tests, so it's mandatory

``` java
features={"src/test/resources/cucumber/unit/UnitTests.feature"}
```
tells Cucumber where features are located, I'm precisely pointing file, but location is also fine

``` java
glue="cucumber.unit.steps"
```
tells Cucumber where to look for classes with steps definitions/implementation


``` java
@Cucumber.Options()
```
tells Cucumber how it should behave, what should it do before tests, during them, and after. You can specify how report should look like, and more. In minimal setup you don't need this annotation at all.

With JBehave it's a bit more code.

``` java
@RunWith(JUnitReportingRunner.class)
public class JBehaveUnitTest extends JUnitStories {
    private Configuration configuration;
    public JBehaveUnitTest() {
        super();
        configuration = new Configuration() {
        };

        configuration.useFailureStrategy(new RethrowingFailure());
        configuration.useKeywords( new LocalizedKeywords( Locale.ENGLISH ) );
        configuration.usePathCalculator( new AbsolutePathCalculator() );
        configuration.useParameterControls( new ParameterControls() );
        configuration.useParameterConverters( new ParameterConverters() );
        configuration.useParanamer( new NullParanamer() );
        configuration.usePendingStepStrategy( new PassingUponPendingStep() );
        configuration.useStepCollector(new MarkUnmatchedStepsAsPending());
        configuration.useStepdocReporter(new PrintStreamStepdocReporter());
        configuration.useStepFinder(new StepFinder());
        configuration.useStepMonitor(new SilentStepMonitor());
        configuration
                .useStepPatternParser(new RegexPrefixCapturingPatternParser());
        configuration.useStoryControls(new StoryControls());
        configuration.useStoryLoader(new LoadFromClasspath());
        configuration.useStoryParser(new RegexStoryParser(configuration
                .keywords()));
        configuration.useStoryPathResolver(new UnderscoredCamelCaseResolver());
        configuration.useStoryReporterBuilder(new StoryReporterBuilder());
        configuration.useViewGenerator(new FreemarkerViewGenerator());

        EmbedderControls embedderControls = configuredEmbedder()
                .embedderControls();
        embedderControls.doBatch(false);
        embedderControls.doGenerateViewAfterStories(false);
        embedderControls.doIgnoreFailureInStories(false);
        embedderControls.doIgnoreFailureInView(false);
        embedderControls.doSkip(false);
        embedderControls.doVerboseFailures(false);
        embedderControls.doVerboseFiltering(false);
        embedderControls.useStoryTimeoutInSecs(300);
        embedderControls.useThreads(1);
    }

    @Override
    public Configuration configuration() {
        return configuration;
    }

    @Override
    public InjectableStepsFactory stepsFactory() {
        return new InstanceStepsFactory(configuration(), new JBehaveUnitTestSteps());
    }
    @Override
    protected List<string> storyPaths() {
        return Arrays.asList( "jbehave/UnitTests.story" );
    }
}</string>
```

Again few words of explanation (I've skipped 99,9% of lines...):

``` java
@RunWith(JUnitReportingRunner.class)
```

tells JUnit which class should be used to run JBehave tests, so it's mandatory

``` java
@Override
protected List<string> storyPaths() {
    return Arrays.asList( "jbehave/UnitTests.story" );
}</string>
```
tells JBehave which story files should be used in this test phase.

And that's basically all for Cucumber and JBehave setup. Sure there is still some customization within pom.xml file... but this will come... hopefully soon.
