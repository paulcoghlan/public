# Kotlin

## Concepts

### Concurrency

- Blocking means `functionA` has to complete before `functionB`
- Suspending functions can be interrupted
- Coroutines are very lightweight, much cheaper than threads
- `runBlocking()` bridges non-blocking to blocking world.  Blocks current thread when waiting so expensive to do -
only typically at very top of application.  Waits for all child threads to complete.
`coroutineScope()` suspends the current thread so it is released for other coroutines (cheap to do)

### Coroutines

- `launch()` used to start a coroutine that doesn’t return a result.  Returns a Job that may be `.cancel()` ed.  
`Job.join()` waits for coroutine to finish.  Exceptions are propagated from the child to the parent coroutine.  
- `async()` starts a coroutine that computes some result.  Exceptions are stored in the Deferred object.
- Some good material that isn’t covered in the official docs:

  - [Coroutines (Part II) – Job, SupervisorJob, Launch and Async by Victor Brandalise](https://victorbrandalise.com/coroutines-part-ii-job-supervisorjob-launch-and-async/)
  - [Are You Handling Exceptions in Kotlin Coroutines Properly](https://www.netguru.com/blog/exceptions-in-kotlin-coroutines)

### Scope Functions

Note: `this` is a lambda receiver (so you don’t need to write `this.`), `it` is a lambda argument `{ arg -> do stuff }`

| Function | Object ref | Returns | Comments |
|----------|------------|---------|----------|
| `.let { }` | `it` | Lambda result | Execute lambda on non-null objects |
| `.run { }` / `run { }` | `run { }` | this |  Lambda result, Running statements where an expression is required |
| `with(contextObj)` | `{ }` | `this` | Lambda result | Grouping function calls on an object |
| `.apply { }` | `this` | Context object |Object configuration | Object configuration and computing the result |
| `.also { }` | `it` | Context object | Additional effects | Running statements where an expression is required |

## Collections

Populate an immutable list

```kt
val uuids = List(1000) { _ -> UUID.randomUUID() }
```

## Pattern Matching

```kt
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> {
        print("x is neither 1 nor 2")
    }
}
```

```kt
val length = str?.let { 
    println("let() called on $it")        
    processNonNullString(it)      // OK: 'it' is not null inside '?.let { }'
    it.length
}
```

## Testing

### Mockk

- Use slots to capture an input

```kt
val slot = slot<Argument>()
coEvery { mockService.doSomething(capture(slot)) } returns Unit
```

Add a mock call counter

```kt
coEvery { mockService.doSomething() } answers {    
    callCounter++
    returnValue
}
```

### MockServer

Set expectation file using annotations:

```kt
@DisplayName("Should test something")
@SetSystemProperty(
    key = "mockserver.initializationJsonPath", value = "path/file.json")
@Test
fun testFunction() {
```

### Typesafe

Override some config:

```kt
private val configOverride = """{     
|service.authToken: "some-token",     
|service.url : "http://localhost:$serverPort" }""".trimMargin()
val testConfig = ConfigFactory.parseString(configOverride)
```

### CSV Annotations

```kt
@ParameterizedTest@CsvSource(
    "a1, a2, a3",    
    "b1, b2, b3",
)
fun testFunction(arg1: String, arg2: arg3, path: String) {
```

### Koin Testing

Handy base class for pulling in a dedicated Typesafe Config object and using a real application module:

```kt
open class ConfigKoinTest : KoinTest {
    private val testConfig = ConfigFactory.load("test-application")

    private val testModule = module {        
      single <Config> { testConfig }    
    }    
      
    @JvmField    
    @RegisterExtension    
    val koinTestExtension = KoinTestExtension.create {        
      modules(testModule)
    }    
    
    @JvmField    
    @RegisterExtension    
    val mockProvider = MockProviderExtension.create { 
      clazz -> mockkClass(clazz)
    }    
    
    @Before    
    fun setUp() {
        startKoin { modules(myRuntimeAppModule) }    
    }

    @After fun after() {
        stopKoin()
    }
}
```

You can then do `val mockMyService = declareMock<MyService>()` and your mock service will be wired in to the test.

### Coroutine Testing

Run tests using a single thread

```kt
val singleThreadDispatcher = Executors.newSingleThreadExecutor().asCoroutineDispatcher()
val scope = CoroutineScope(singleThreadDispatcher)
runBlocking(singleThreadDispatcher) {
  ...
```

Wait for a condition to happen

```kt
withTimeout(delayMillsecs) {    
    while (<some expression>) {
        yield()
    }
}
```

Test Resources

```kt
MyClass.javaClass.classLoader.getResource("directory/somefile.json")!!.readText()
```
