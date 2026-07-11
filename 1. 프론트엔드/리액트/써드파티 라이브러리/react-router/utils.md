# react-router utils

## generatePath
매개변수가 삽입된 경로를 반환

```tsx
import { generatePath } from "react-router";

generatePath("/users/:id", { id: "123" }); // "/users/123"
```

> generatePath를 사용하면 type-safe하게 관리할 수 있다.  
> https://confeti.palms.blog/type-safe-routing