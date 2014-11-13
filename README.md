Xtext - bug #437519
===================

Demonstrates bug #437519 of the Xtext v2.7 : https://bugs.eclipse.org/bugs/show_bug.cgi?id=437519

This project was created using following three simple steps:

* Run Xtext wizard choosing domainmodel example (see [commit 1](https://github.com/borisbrodski/XtextBug437519/commit/40c6b6a91824cfa613f6768542c76ef7252a1433))
* Make Grammar use `XbaseWithAnnotations`, add `XAnnotation` to the operations (see [commit 2](https://github.com/borisbrodski/XtextBug437519/commit/72bd3ad5e504c7d37aad223180b4ca40f2696ed8))
* Generate annotations on operations, generate a simple interface, fix CompilerTests (see [commit 3](https://github.com/borisbrodski/XtextBug437519/commit/94b84c2975106132bd3cbcb4f296f7dcf59512ea))

Now running `CompilerTest.compareGeneratedJavaWithAnnotations()` test we get `NullPointerException`. (see below)
Modifying `DomainmodelJvmModelInferrer.xtend` so, that new interface methods are associated with the `entity` rather with the `f` (operation) we get the test pass.

Consider following part of the `DomainmodelJvmModelInferrer.xtend`

    // NullPointerException in CompilerTest.compareGeneratedJavaWithAnnotations()
    members += f.toMethod(f.name, f.type ?: inferredType) []

    // Works, but messes up the navigation making users of my plug-in mad!
    // members += entity.toMethod(f.name, f.type ?: inferredType) []


NullPointerException
====================


    java.lang.RuntimeException: java.lang.NullPointerException
    	at org.eclipse.xtext.xbase.compiler.CompilationTestHelper.compile(CompilationTestHelper.java:190)
    	at org.eclipse.xtext.xbase.compiler.CompilationTestHelper.compile(CompilationTestHelper.java:169)
    	at org.eclipse.xtext.example.domainmodel.tests.CompilerTest.compareGeneratedJavaWithAnnotations(CompilerTest.java:291)
    	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    	at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
    	at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
    	at java.lang.reflect.Method.invoke(Unknown Source)
    	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:47)
    	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
    	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:44)
    	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
    	at org.junit.rules.ExternalResource$1.evaluate(ExternalResource.java:48)
    	at org.junit.rules.RunRules.evaluate(RunRules.java:20)
    	at org.eclipse.xtext.junit4.XtextRunner$1.evaluate(XtextRunner.java:49)
    	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:271)
    	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:70)
    	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:50)
    	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:238)
    	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:63)
    	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:236)
    	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:53)
    	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:229)
    	at org.junit.runners.ParentRunner.run(ParentRunner.java:309)
    	at org.eclipse.jdt.internal.junit4.runner.JUnit4TestReference.run(JUnit4TestReference.java:50)
    	at org.eclipse.jdt.internal.junit.runner.TestExecution.run(TestExecution.java:38)
    	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:459)
    	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:675)
    	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:382)
    	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:192)
    Caused by: java.lang.NullPointerException
    	at org.eclipse.xtext.xbase.compiler.LiteralsCompiler.toJavaExpression(LiteralsCompiler.java:79)
    	at org.eclipse.xtext.xbase.compiler.LiteralsCompiler._toJavaExpression(LiteralsCompiler.java:71)
    	at org.eclipse.xtext.xbase.compiler.LiteralsCompiler.internalToConvertedExpression(LiteralsCompiler.java:39)
    	at org.eclipse.xtext.xbase.compiler.FeatureCallCompiler.internalToConvertedExpression(FeatureCallCompiler.java:95)
    	at org.eclipse.xtext.xbase.compiler.XbaseCompiler.internalToConvertedExpression(XbaseCompiler.java:321)
    	at org.eclipse.xtext.xbase.compiler.TypeConvertingCompiler.internalToConvertedExpression(TypeConvertingCompiler.java:101)
    	at org.eclipse.xtext.xbase.compiler.TypeConvertingCompiler.internalToJavaExpression(TypeConvertingCompiler.java:48)
    	at org.eclipse.xtext.xbase.compiler.AbstractXbaseCompiler.toJavaExpression(AbstractXbaseCompiler.java:481)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator$43.apply(JvmModelGenerator.java:1849)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator$43.apply(JvmModelGenerator.java:1)
    	at org.eclipse.xtext.xbase.lib.ObjectExtensions.operator_doubleArrow(ObjectExtensions.java:139)
    	at org.eclipse.xtext.xbase.compiler.LoopExtensions.forEachWithShortcut(LoopExtensions.java:54)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator._toJavaLiteral(JvmModelGenerator.java:1852)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.toJavaLiteral(JvmModelGenerator.java:2038)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.toJava(JvmModelGenerator.java:1647)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator$30.apply(JvmModelGenerator.java:1618)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator$30.apply(JvmModelGenerator.java:1)
    	at org.eclipse.xtext.xbase.lib.ObjectExtensions.operator_doubleArrow(ObjectExtensions.java:139)
    	at org.eclipse.xtext.xbase.compiler.LoopExtensions.forEach(LoopExtensions.java:34)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.generateAnnotation(JvmModelGenerator.java:1621)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator$28.apply(JvmModelGenerator.java:1598)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator$28.apply(JvmModelGenerator.java:1)
    	at org.eclipse.xtext.xbase.compiler.ErrorSafeExtensions.forEachSafely(ErrorSafeExtensions.java:119)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.generateAnnotations(JvmModelGenerator.java:1601)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator._generateMember(JvmModelGenerator.java:897)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.generateMember(JvmModelGenerator.java:2011)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator$2.apply(JvmModelGenerator.java:316)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator$2.apply(JvmModelGenerator.java:1)
    	at org.eclipse.xtext.xbase.lib.ObjectExtensions.operator_doubleArrow(ObjectExtensions.java:139)
    	at org.eclipse.xtext.xbase.compiler.LoopExtensions$1.apply(LoopExtensions.java:39)
    	at org.eclipse.xtext.xbase.lib.IteratorExtensions.forEach(IteratorExtensions.java:360)
    	at org.eclipse.xtext.xbase.lib.IterableExtensions.forEach(IterableExtensions.java:331)
    	at org.eclipse.xtext.xbase.compiler.LoopExtensions.forEach(LoopExtensions.java:42)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.generateMembersInBody(JvmModelGenerator.java:320)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator._generateBody(JvmModelGenerator.java:278)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.generateBody(JvmModelGenerator.java:1983)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.generateType(JvmModelGenerator.java:218)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator._internalDoGenerate(JvmModelGenerator.java:208)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.internalDoGenerate(JvmModelGenerator.java:1966)
    	at org.eclipse.xtext.xbase.compiler.JvmModelGenerator.doGenerate(JvmModelGenerator.java:189)
    	at org.eclipse.xtext.xbase.compiler.CompilationTestHelper$Result.doGenerate(CompilationTestHelper.java:459)
    	at org.eclipse.xtext.xbase.compiler.CompilationTestHelper.compile(CompilationTestHelper.java:187)
    	... 28 more
    

