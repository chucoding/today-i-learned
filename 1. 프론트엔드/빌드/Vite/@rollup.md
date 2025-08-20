# @rollup
프로덕션 빌드 시 번들링을 담당하는 핵심 모듈

### 주요 역할
1. 프로덕션 빌드 번들링  
  - vite build 명령어 실행 시 사용됨
  - 개발 환경에서는 사용되지 않고, 프로덕션 빌드에서만 활성화
2. ES 모듈 번들링
  - ES6+ 모듈을 하나의 번들로 묶어주는 역할
  - Tree-shaking을 통한 사용하지 않는 코드 제거
  - 코드 분할(Code Splitting) 지원
3. Vite의 아키텍처 변화
  - 이전에는 Go로 작성된 Esbuild를 사용
  - 최근에는 Rollup을 사용하여 번들링
  - 향후 Rollup의 후속작인 Rolldown이 빌드 성능을 크게 향상시킬 예정

### 장점
- 개발 환경: Native ESM을 직접 사용하여 빠른 HMR 제공
- 프로덕션 환경: @rollup을 통해 최적화된 번들 생성


### 설정 예시
```ts
// vite.config.ts
export default defineConfig({
  resolve: {
    alias: {
      '@': '/src',
      '@components': '/src/components',
      '@utils': '/src/utils'
    }
  },
  build: {
    rollupOptions: {
      // Rollup 전용 설정
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom']
        }
      }
    }
  }
})
```

### Rollup Alias 설정 방법

Vite에서 `resolve.alias`를 통해 Rollup의 alias 기능 사용가능

```ts
// Vite에서 find/replacement 형태로 alias 설정
export default defineConfig({
  resolve: {
    alias: [
      { find: '@', replacement: '/src' },
      { find: '@components', replacement: '/src/components' },
      { find: '@utils', replacement: '/src/utils' },
      // RegExp도 사용 가능
      { find: /^@lib\/(.*)/, replacement: '/src/lib/$1' }
    ]
  }
})

// 또는 객체 형태로도 설정 가능
export default defineConfig({
  resolve: {
    alias: {
      '@': '/src',
      '@components': '/src/components',
      '@utils': '/src/utils'
    }
  }
})
```

**참고**: Vite는 내부적으로 `@rollup/plugin-alias`를 사용하므로, 별도의 플러그인 설치 없이도 Rollup의 모든 alias 기능을 활용할 수 있습니다.

### vite-tsconfig-paths
alias를 설정하면 tsconfig 설정에도 똑같이 추가해줘야하는 번거로움이 있는데 이 때 `vite-tsconfig-paths`를 사용하면 tsconfig만 수정해도 vite 번들러에 동시 설정됨.

```powershell
yarn add vite-tsconfig-paths
```

tsconfig.json
```json
{
    "compilerOptions": {
        "paths": {
            "@shared/*": ["src/shared/*"],
            ...
        }
    }
}
```

vite.config.ts
```ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react-swc';
import tsconfigPaths from 'vite-tsconfig-paths';

export default defineConfig({
    plugins: [tsconfigPaths(), react()]
})
```