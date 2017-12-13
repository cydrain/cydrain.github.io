---
title: Malloc Page
date: 2017-12-10 18:46:38
tags:
categories: Memory
---

Malloc 一个单独的内存页。

malloc 的时候，申请两个内存页的空间，一定包含一个完整的单独内存页。
申请到地址为原始地址`org_ptr`，对原始地址的低地址位清零，得到单独的内存页地址`page_ptr`，
原始地址需要保存在`page_ptr - 1`的地址上。
```c
void* mallocPage(void)
{
    void *org_ptr = NULL, *page_ptr = NULL;
    ulong *addr_ptr = NULL;

    org_ptr = malloc(PAGE_SIZE * 2);
    if (org_ptr == NULL) {
        printf("No enough memory!\n");
        return NULL;
    }

    page_ptr = (void*)(((ulong)org_ptr + PAGE_SIZE) & PAGE_MASK);
    addr_ptr = (ulong*)page_ptr - 1;
    *addr_ptr = (ulong)org_ptr;
    printf("org_ptr = %p, addr_ptr = %p, *addr_ptr = %x, page_ptr = %p\n",
            org_ptr, addr_ptr, *addr_ptr, page_ptr);
    
    return page_ptr;
}
```

free 的时候，通过`page_ptr - 1`得到原始的内存地址，再 free 。
```c
void freePage(void* ptr)
{
    void *org_ptr = NULL;

    if (ptr == NULL) {
        printf("Invalid ptr!\n");
        return;
    }
    
    org_ptr = (void*)*((ulong*)ptr - 1);
    printf("Free %p\n", org_ptr);

    free(org_ptr);
}
```

完整代码请参考: [https://github.com/cydrain/Coding_Practice/blob/master/myCode/mallocPage/mallocPage2.c]

