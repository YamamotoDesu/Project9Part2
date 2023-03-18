# Project9Part2
<img width="300" src="https://user-images.githubusercontent.com/47273077/225537686-52390374-c065-4c25-9155-5f60376d15d2.png">

<img width="300" src="https://user-images.githubusercontent.com/47273077/225538008-14d6c5ae-aaff-40b5-a1ce-c964c891198c.gif">

```swift
struct Float: Shape {
    var petalOffset = -20.0
    var petalWidth = 100.0
    
    func path(in rect: CGRect) -> Path {
        var path = Path()
        
        for number in stride(from: 0, to: Double.pi * 2, by: Double.pi / 8) {
            let rotation = CGAffineTransform(rotationAngle: number)
            let postion = rotation.concatenating(CGAffineTransform(translationX: rect.width / 2, y: rect.height / 2))
            
            let originalPetal = Path(ellipseIn: CGRect(x: petalOffset, y: 0, width: petalWidth, height: rect.width / 2))
            let rotatedPetal = originalPetal.applying(postion)
            
            path.addPath(rotatedPetal)
        }
        return path
    }
}

struct ContentView: View {
    @State private var petalOffset = -20.0
    @State private var petalWidth = 100.0
    
    var body: some View {
        VStack {
            Float(petalOffset: petalOffset, petalWidth: petalWidth)
                .fill(.red, style: FillStyle(eoFill: true))
            
            Text("Offset")
            Slider(value: $petalOffset, in: -40...40)
                .padding([.horizontal, .bottom])
            
            Text("Width")
            Slider(value: $petalWidth, in: 0...100)
                .padding(.horizontal)
        }
    }
}
```

<img width="300" alt="スクリーンショット 2023-03-16 16 50 40" src="https://user-images.githubusercontent.com/47273077/225549924-7b95ebbc-c37a-44b0-acc9-987f95141ec7.png">

```swift
var body: some View {
        Text("Hello World!")
            .frame(width: 300, height: 300)
            .border(ImagePaint(image: Image("Example"), sourceRect: CGRect(x: 0, y: 0.25, width: 1, height: 0.5), scale: 0.2), width: 50)
    }
```

<img width="300" alt="スクリーンショット 2023-03-16 16 58 45" src="https://user-images.githubusercontent.com/47273077/225551702-9bb131fd-1ea8-44d1-9700-4b6541900310.png">

```swift
    var body: some View {
        Capsule()
            .strokeBorder(ImagePaint(image: Image("Example"), sourceRect: CGRect(x: 0, y: 0.4, width: 1, height: 0.2), scale: 0.1), lineWidth: 20)
            .frame(width: 300, height: 300)
    }
```

<img width="300" alt="スクリーンショット 2023-03-17 9 50 49" src="https://user-images.githubusercontent.com/47273077/225783804-8b0a5b9e-bc23-46c0-9a38-5efcec4b96de.png">

### Very slow
<img width="300" alt="スクリーンショット 2023-03-17 9 50 49" src="https://user-images.githubusercontent.com/47273077/225783804-8b0a5b9e-bc23-46c0-9a38-5efcec4b96de.png">

<img width="300" alt="スクリーンショット 2023-03-17 9 50 49" src="https://user-images.githubusercontent.com/47273077/225784034-bf5e1405-43bb-4328-96c2-58b7675ef413.gif">

```swift
struct ColorCyclingCircle: View {
    var amount = 0.0
    var steps = 100
    
    var body: some View {
        ZStack {
            ForEach(0..<steps) { value in
                Circle()
                    .inset(by: Double(value))
                    .strokeBorder(
                        LinearGradient(
                            gradient: Gradient(colors: [
                                color(for: value, brightness: 1),
                                color(for: value, brightness: 0.5),
                                
                            ]),
                            startPoint: .top,
                            endPoint: .bottom
                        ),
                        lineWidth: 2
                    )
            }
        }
    }
    
    func color(for value: Int, brightness: Double) -> Color {
        var targetHue = Double(value) / Double(steps) + amount
        
        if targetHue > 1 {
            targetHue -= 1
        }
        
        return Color(hue: targetHue, saturation: 1, brightness: brightness)
    }
}

struct ContentView: View {
    @State private var colorCycle = 0.0
    
    var body: some View {
        VStack {
            ColorCyclingCircle(amount: colorCycle)
                .frame(width: 300, height: 300)
            
            Slider(value: $colorCycle)
        }
    }
}
```

### Enhance Performance with drawingGroup
<img width="300" alt="スクリーンショット 2023-03-17 9 50 49" src="https://user-images.githubusercontent.com/47273077/225785640-89b1cc75-eafc-4f84-aa72-209a0007ddc5.gif">

```swift
    var body: some View {
        ZStack {
            ForEach(0..<steps) { value in
                Circle()
                    .inset(by: Double(value))
                    .strokeBorder(
                        LinearGradient(
                            gradient: Gradient(colors: [
                                color(for: value, brightness: 1),
                                color(for: value, brightness: 0.5),
                                
                            ]),
                            startPoint: .top,
                            endPoint: .bottom
                        ),
                        lineWidth: 2
                    )
            }
        }
        .drawingGroup() // added
    }
```

Important: The drawingGroup() modifier is helpful to know about and to keep in your arsenal as a way to solve performance problems when you hit them, but you should not use it that often. Adding the off-screen render pass might slow down SwiftUI for simple drawing, so you should wait until you have an actual performance problem before trying to bring in drawingGroup().

<img width="300" alt="スクリーンショット 2023-03-17 10 31 36" src="https://user-images.githubusercontent.com/47273077/225789267-0925743b-ed87-4d59-bf7b-f3281e422fb8.png">

```swift
      ZStack {
            Image("PaulHudson")
            
            Rectangle()
                .fill(.red)
                .blendMode(.multiply)
        }
```

```swift
            Image("PaulHudson")
                .colorMultiply(.red)
```

<img width="300" alt="スクリーンショット 2023-03-18 11 23 37" src="https://user-images.githubusercontent.com/47273077/226078234-0ce1735e-c821-4921-827f-3473dfbb00df.png">

```swift
 var body: some View {
        VStack {
            ZStack {
                Circle()
                    .fill(.red)
                    .frame(width: 200 * amount)
                    .offset(x: -50, y: -80)
                    .blendMode(.screen)

                Circle()
                    .fill(.green)
                    .frame(width: 200 * amount)
                    .offset(x: 50, y: -80)
                    .blendMode(.screen)

                Circle()
                    .fill(.blue)
                    .frame(width: 200 * amount)
                    .blendMode(.screen)
            }
            .frame(width: 300, height: 300)
            
            Slider(value: $amount)
                .padding()
        }
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .background(.black)
        .ignoresSafeArea()
    }
```


If you’re particularly observant, you might notice that the fully blended color in the center isn’t quite white – it’s a very pale lilac color. The reason for this is that Color.red, Color.green, and Color.blue aren’t fully those colors; you’re not seeing pure red when you use Color.red. Instead, you’re seeing SwiftUI’s adaptive colors that are designed to look good in both dark mode and light mode, so they are a custom blend of red, green, and blue rather than pure shades.


If you want to see the full effect of blending red, green, and blue, you should use custom colors like these three:

```swift
.fill(Color(red: 1, green: 0, blue: 0))
.fill(Color(red: 0, green: 1, blue: 0))
.fill(Color(red: 0, green: 0, blue: 1))
```

![Uploading スクリーンショット 2023-03-18 11.29.32.png…]()

```swift
 ZStack {
                Circle()
                    .fill(Color(red: 1, green: 0, blue: 0))
                    .frame(width: 200 * amount)
                    .offset(x: -50, y: -80)
                    .blendMode(.screen)

                Circle()
                    .fill(Color(red: 0, green: 1, blue: 0))
                    .frame(width: 200 * amount)
                    .offset(x: 50, y: -80)
                    .blendMode(.screen)

                Circle()
                    .fill(Color(red: 0, green: 0, blue: 1))
                    .frame(width: 200 * amount)
                    .blendMode(.screen)
            }
            .frame(width: 300, height: 300)
 ```

<img width="300" alt="スクリーンショット 2023-03-18 11 36 55" src="https://user-images.githubusercontent.com/47273077/226079274-55ed0983-1d2c-411f-8a29-ac6767d855f2.gif">

```swift
 @State private var amount = 0.0
    
    var body: some View {
        VStack {
            Image("PaulHudson")
                .resizable()
                .scaleEffect()
                .frame(width: 200, height: 200)
                .saturation(amount)
                .blur(radius: (1 - amount) * 20)
            
            Slider(value: $amount)
                .padding()
        }
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .background(.black)
        .ignoresSafeArea()
    }
 ```
 
 
<img width="300" alt="スクリーンショット 2023-03-18 11 36 55" src="https://user-images.githubusercontent.com/47273077/226081172-75314b95-a20e-45ef-a7fd-1097b9c42d36.gif">

 ```swift
 struct Trapezoid: Shape {
    var insetAmount: Double
    
    func path(in rect: CGRect) -> Path {
        var path = Path()
        
        path.move(to: CGPoint(x: 0, y: rect.maxY))
        path.addLine(to: CGPoint(x: insetAmount, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.maxY - insetAmount, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY))
        path.addLine(to: CGPoint(x: 0, y: rect.maxY))
        
        return path
    }
    
}

struct ContentView: View {
    @State private var insetAmount = 50.0
    
    var body: some View {
        Trapezoid(insetAmount: insetAmount)
            .frame(width: 200, height: 200)
            .onTapGesture {
                withAnimation {
                    insetAmount = Double.random(in: 10...90)
                }
            }
    }
}
```

Now run it again, and… nothing has changed. We’ve asked for animation, but we aren’t getting animation – what gives?

When looking at animations previously, I asked you to add a call to print() inside the body property, then said this:

”What you should see is that it prints out 2.0, 3.0, 4.0, and so on. At the same time, the button is scaling up or down smoothly – it doesn’t just jump straight to scale 2, 3, and 4. What’s actually happening here is that SwiftUI is examining the state of our view before the binding changes, examining the target state of our views after the binding changes, then applying an animation to get from point A to point B.”

So, as soon as insetAmount is set to a new random value, it will immediately jump to that value and pass it directly into Trapezoid – it won’t pass in lots of intermediate values as the animation happens. This is why our trapezoid jumps from inset to inset; it has no idea an animation is even happening.

We can fix this in only four lines of code, one of which is just a closing brace. However, even though this code is simple, the way it works might bend your brain.

First, the code – add this new computed property to the Trapezoid struct now:

```swift
var animatableData: Double {
    get { insetAmount }
    set { insetAmount = newValue }
}
```

<img width="300" alt="スクリーンショット 2023-03-18 11 36 55" src="https://user-images.githubusercontent.com/47273077/226083483-b3059aed-9d49-4bc5-8004-fdfc7225803d.gif">

```swift
struct Trapezoid: Shape {
    var insetAmount: Double
    var animatableData: Double {
        get { insetAmount }
        set { insetAmount = newValue }
    }
    
    func path(in rect: CGRect) -> Path {
        var path = Path()
        
        path.move(to: CGPoint(x: 0, y: rect.maxY))
        path.addLine(to: CGPoint(x: insetAmount, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.maxY - insetAmount, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY))
        path.addLine(to: CGPoint(x: 0, y: rect.maxY))
        
        return path
    }
    
}

struct ContentView: View {
    @State private var insetAmount = 50.0
    
    var body: some View {
        Trapezoid(insetAmount: insetAmount)
            .frame(width: 200, height: 200)
            .onTapGesture {
                withAnimation {
                    insetAmount = Double.random(in: 10...90)
                   
                }
            }
    }
}
```

<img width="300" alt="スクリーンショット 2023-03-18 11 36 55" src="https://user-images.githubusercontent.com/47273077/226081741-145cf785-9b21-4210-9517-3bac61406e8c.gif">

As with simpler shapes, the solution here is to implement an animatableData property that will be set with intermediate values as the animation progresses. Here, though, there are two catches:

We have two properties that we want to animate, not one.
Our row and column properties are integers, and SwiftUI can’t interpolate integers.


```swift
struct Checkerboard: Shape {
    var rows: Int
    var columns: Int
    
    func path(in rect: CGRect) -> Path {
        var path = Path()
        
        let rowSize = rect.height / Double(rows)
        let columnSize = rect.width / Double(columns)
        
        for row in 0..<rows {
            for column in 0..<columns {
                if (row + column).isMultiple(of: 2) {
                    let startX = columnSize * Double(column)
                    let startY = rowSize * Double(row)
                    
                    let rect = CGRect(x: startX, y: startY, width: columnSize, height: rowSize)
                    path.addRect(rect)
                }
            }
        }
        
        return path
    }
}

struct ContentView: View {
    @State private var rows = 4
    @State private var columns = 4
    
    var body: some View {
       Checkerboard(rows: rows, columns: columns)
            .onTapGesture {
                withAnimation(.linear(duration: 3)) {
                    rows = 8
                    columns = 16
                }
            }
    }
}
```
