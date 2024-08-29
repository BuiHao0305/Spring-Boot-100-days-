
### 1.Quản lý Exception tập trung và Validation:
__Tác Dụng__:Quản lý nhiều endpoint trả về lỗi tập trung các exception về 1 chỗ 
Cách thực hiện:
`• @ControllerAdvice`: Annotation này được sử dụng để định nghĩa một lớp xử lý ngoại lệ toàn cục. Bất kỳ lỗi nào xảy ra trong các controller sẽ được xử lý bởi các phương thức trong lớp này.
`• @ExceptionHandler`: Annotation này được sử dụng trên các phương thức trong lớp @ControllerAdvice để xử lý các loại ngoại lệ cụ thể.
```c
//khi có exception xảy ra class này chịu trách nhiệm Handler message đó
// khi có exception xảy ra class này chịu trách nhiệm Handler message đó
    @ExceptionHandler(value = RuntimeException.class)
    ResponseEntity<ApiReponse> handleRuntimeException(RuntimeException exception) {
        return ResponseEntity.badRequest().body(exception.getMessage());
    }
```
### 2.Validation
 
__1. Bảo vệ ứng dụng:__ Ngăn chặn các dữ liệu không hợp lệ hoặc độc hại xâm nhập vào hệ thống, giúp giảm thiểu nguy cơ tấn công bảo mật như SQL injection hoặc XSS.
__2. Cải thiện trải nghiệm người dùng:__ Người dùng sẽ nhận được phản hồi tức thì nếu dữ liệu họ nhập không hợp lệ, giúp họ chỉnh sửa lại dữ liệu đúng cách.
__3.	Đảm bảo tính toàn vẹn của dữ liệu:__ Dữ liệu sẽ được kiểm tra tính đúng đắn trước khi lưu vào cơ sở dữ liệu, giúp duy trì tính nhất quán và chính xác của dữ liệu.
__4.	Giảm lỗi runtime:__ Việc kiểm tra dữ liệu đầu vào trước giúp phát hiện và ngăn chặn lỗi xảy ra trong quá trình xử lý logic.
__5.	Sử dụng các annotation từ javax.validation.constraints:__ Các annotation như `@NotNull`, `@Size`, `@Email`, `@Min`, `@Max` được sử dụng để áp dụng các ràng buộc lên các trường trong lớp DTO hoặc entity.
 
__Thêm maven ở pom.xml__
##### Maven:
```c
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

__Chuột phải__ -> __maven__ -> __reload project.__

Sau đó:
```c
public class UserDto {
    private int id;
    private int mark;
    @Size(min=3, message = "Name must be 3 character")
    private String name;
```
Thêm annotation: `@Size` ở UserDto
Sau đó annotation: `@Valid` ở Controller 

```c
@PostMapping("/User")
    ApiReponse<User> createUser(@RequestBody @Valid UserDto request) {
        return userSerivce.createUser(request);
    }
```
__Phương thức handleRuntimeException(RuntimeException exception)__
__•	Annotation__: `@ExceptionHandler(value = RuntimeException.class)`
__•	Chức năng__: Phương thức này sẽ bắt mọi ngoại lệ thuộc loại RuntimeException hoặc các lớp con của nó. Khi một ngoại lệ loại này xảy ra trong ứng dụng, phương thức này sẽ xử lý và trả về một phản hồi (response) với mã trạng thái HTTP 400 (Bad Request) cùng với thông báo lỗi được lấy từ exception.getMessage().
__•	Ví dụ:__ Khi một lỗi xảy ra trong mã của bạn, chẳng hạn như `NullPointerException`, `IllegalArgumentException`, hay bất kỳ ngoại lệ RuntimeException nào khác, phương thức này sẽ được gọi để xử lý và trả về thông báo lỗi cho người dùng.
```c
@ExceptionHandler(value = RuntimeException.class)
ResponseEntity<String> handleRuntimeException(RuntimeException exception){
	Return ResponseEntity.badRequest().body(exception.getMessage());
}

```
__2. Phương thức handlingValidationException(MethodArgumentNotValidException exception)__
__•	Annotation:__ `@ExceptionHandler(value = MethodArgumentNotValidException.class)`
__•	Chức năng:__ Phương thức này được sử dụng để xử lý ngoại lệ `MethodArgumentNotValidException`, thường xảy ra khi dữ liệu đầu vào không thỏa mãn các ràng buộc validation. Ngoại lệ này xảy ra khi một hoặc nhiều tham số của một phương thức không hợp lệ theo các annotation validation (như `@Size`, `@NotNull`, `@Email`, v.v.).
__•	Cách hoạt động:__ Khi ngoại lệ này xảy ra, phương thức sẽ trả về mã trạng thái HTTP 400 (Bad Request) cùng với thông báo lỗi từ trường không hợp lệ đầu tiên, thông qua `exception.getFieldError().getDefaultMessage()`.
__•	Ví dụ:__ Nếu người dùng gửi một yêu cầu với một tên quá ngắn (ví dụ dưới 3 ký tự), ngoại lệ này sẽ xảy ra và phương thức sẽ trả về thông báo lỗi tương ứng.
```c
@ExceptionHandler(value =MethodArgumentNotValidException.class)
ResponseEntity<String> handlingValidationException(MethodArgumentNotValidException exception){
	Return ResponseEntity.badRequest().body(exception.getFieldError().getDefaultMessage());
}
```
 







