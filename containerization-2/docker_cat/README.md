# Sự khác biệt giữa CMD và ENTRYPOINT trong Dockerfile

**ENTRYPOINT** và **CMD** đều là các instruction trong Dockerfile được sử dụng để thiết lập lệnh mặc định cho container khi chạy image. Tuy nhiên, chúng có những khác biệt nhất định:

- **ENTRYPOINT** được sử dụng để thiết lập lệnh chính của container, trong khi **CMD** được sử dụng để cung cấp các tham số cho lệnh đó.

- Nếu trong Dockerfile có nhiều hơn một instruction **ENTRYPOINT**, chỉ instruction cuối cùng sẽ được sử dụng.

- Nếu trong Dockerfile có nhiều hơn một instruction **CMD**, chỉ instruction cuối cùng sẽ được sử dụng.

- **ENTRYPOINT** có thể được ghi đè bằng cách sử dụng cờ `--entrypoint` khi chạy lệnh docker run.

- **CMD** có thể được ghi đè bằng cách truyền tham số trực tiếp vào lệnh docker run.
