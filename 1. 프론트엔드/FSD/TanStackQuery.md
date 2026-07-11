# FSD - TanStack Query
> https://feature-sliced.design/kr/docs/guides/tech/with-react-query

## QueryClient

@/app/providers/query-provider.tsx
```ts

import { type ReactNode } from 'react';
import { QueryClient, QueryClientProvider, MutationCache, QueryCache } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import { toast } from 'sonner';

interface QueryProviderProps {
    children: ReactNode;
    client: QueryClient;
}

const queryClient = new QueryClient({
    queryCache: new QueryCache({
        onError: error => {
            toast.error(error.message);
        },
    }),
    mutationCache: new MutationCache({
        onError: error => {
            toast.error(error.message);
        },
    }),
    defaultOptions: {
        queries: {
            staleTime: 5 * 60 * 1000,
            gcTime: 5 * 60 * 1000,
        },
    },
});

export const QueryProvider = ({ client, children }: QueryProviderProps) => {
    return (
        <QueryClientProvider client={ client }>
            { children }
            <ReactQueryDevtools />
        </QueryClientProvider>
    );
};
```

## useQuery
TanStack Query의 queryKey, queryFn, queryOptions, query factory, 서버 요청 함수는 api 세그먼트에 두는 것이 현재 FSD 공식 가이드