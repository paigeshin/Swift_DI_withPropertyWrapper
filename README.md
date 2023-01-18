# Swift_DI_withPropertyWrapper

### Resolver 
```swift 
class Resolver {
    private var dependencies: [String: AnyObject] = [String: AnyObject]()
    private static var shared: Resolver = Resolver()
    
    static func register<T>(_ dependency: T) {
        print(String(describing: T.self) + " is registered")
        Resolver.shared.register(dependency)
    }
    
    static func resolve<T>() -> T {
        print(String(describing: T.self) + " is resolved")
        return Resolver.shared.resolve()
    }
    
    private func register<T>(_ dependency: T) {
        let key: String = String(describing: T.self)
        self.dependencies[key] = dependency as AnyObject
    }
    
    private func resolve<T>() -> T {
        let key = String(describing: T.self)
        let dependency: T? = self.dependencies[key] as? T
        
        assert(dependency != nil, "No dependency found")
        
        return dependency!
    }
}
```

### Inject 
```swift
@propertyWrapper
struct Inject<T> {
    var wrappedValue: T
    
    init() {
        self.wrappedValue = Resolver.resolve()
    }
}
```

### App
```swift 
import SwiftUI

@main
struct DIWithPropertyWrapperApp: App {
    
    init() {
        Resolver.register(PrintService() as PrintServiceProtocol)
    }
    
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

### ContentView 
```swift 
import SwiftUI

struct ContentView: View {
    
    @Inject<PrintServiceProtocol> private var printService: PrintServiceProtocol

    var body: some View {
        VStack {
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundColor(.accentColor)
            Text("Hello, world!")
        }
        .padding()
        .onAppear {
            self.printService.printSomething(text: "Hahahaha")
        }
    }
}



struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```
