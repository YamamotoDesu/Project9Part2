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

