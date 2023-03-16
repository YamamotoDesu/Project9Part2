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
