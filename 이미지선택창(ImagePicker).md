# SwiftUI-Note

## ImagePicker

`UIViewControllerRepresentable` 프로토콜을 사용하여 `UIKit`의 `UIImagePickerController`을 `SwiftUI` 를 위한 새로운 뷰로 선언할 수 있습니다.
(`UIViewControllerRepresentable` 프로토콜은 `View` 프로토콜을 준수합니다)

`ImagePicker`의 전체 코드 보기

<details>

```swift
import UIKit
import SwiftUI

struct ImagePicker: UIViewControllerRepresentable {
    @Environment(\.presentationMode) private var presentationMode
    let sourceType: UIImagePickerController.SourceType
    let onPickedImage: (UIImage) -> ()
    
    class Coordinator: NSObject, UINavigationControllerDelegate, UIImagePickerControllerDelegate {
        let imagePicker: ImagePicker
        
        init(imagePicker: ImagePicker) {
            self.imagePicker = imagePicker
        }
        
        func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
            guard let image = info[.originalImage] as? UIImage else { return }
            self.imagePicker.onPickedImage(image)
            self.imagePicker.presentationMode.wrappedValue.dismiss()
        }
        
        func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
            self.imagePicker.presentationMode.wrappedValue.dismiss()
        }
    }
    
    func makeUIViewController(context: Context) -> UIImagePickerController {
        let imagePickerController = UIImagePickerController()
        imagePickerController.delegate = context.coordinator
        return imagePickerController
    }
    
    func updateUIViewController(_ uiViewController: UIImagePickerController, context: Context) {
        
    }
    
    func makeCoordinator() -> Coordinator {
        Coordinator(imagePicker: self)
    }
}
```

</details>

### 사용방법

```swift
struct NewDrinkView: View {
    // 음료 이미지
    @State private var drinkImage: Image = Image("noImage")
    // 이미지선택창 선택 여부
    @State private var presentsImagePicker = false
    // 카메라 선택 여부
    @State private var onCamera = false
    // 사진 앨범 선택 여부
    @State private var onPhotoLibrary = false
    
    var body: some View {
        VStack {
            // 음료이미지 선택 시 이미지선택창 띄우기
            drinkImage
                .onTabGesture { presentsImagePicker = true }
        }
        // 카메라 선택
        .sheet(isPresented: $onCamera) {
            ImagePicker(sourceType: .camera) { (pickedImage) in
                drinkImage.image = Image(uiImage: pickedImage)
            }
        }
        // 사진 앨범 선택
        .sheet(isPresented: $onPhotoLibrary) {
            ImagePicker(sourceType: .photoLibrary) { (pickedImage) in
                drinkImage.image = Image(uiImage: pickedImage)
            }
        }
        .actionSheet(isPresented: $presentsImagePicker) {
            ActionSheet(
                title: Text("이미지 선택하기"),
                message: nil,
                buttons: [
                    .default(
                        Text("카메라"),
                        action: { onCamera = true }
                    ),
                    .default(
                        Text("사진 앨범"),
                        action: { onPhotoLibrary = true }
                    ),
                    .cancel(
                        Text("돌아가기")
                    )
                ]
            )
        }
    }
}
```
