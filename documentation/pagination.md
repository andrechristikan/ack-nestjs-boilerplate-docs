
# Pagination

> Store as global module

ack-nestjs-boilerplate-mongoose create `PaginationModule` for handle pagination and the location is in `src/utils/pagination/pagination.module.ts`.

```txt
src
  └── utils
      └── pagination 
          └── pagination.module.ts
```

The pagination can be (very) costly and requires the server because of deep `skip` amount of data. So ack-nestjs-boilerplate-mongoose provide limitation or rule.

- `Max Page` = 20
- `Max Per Page` = 100

## Usage

To use pagination, you can use `abstract class` and implement this for `validation layer`. For example, we will create pagination for `User List`

```typescript
// You also can add some filter too in here

export class UserListDto implements PaginationListAbstract {
    @Expose()
    @PaginationDefaultSearch()
    readonly search?: string;

    @Expose()
    @PaginationDefaultAvailableSearch(USER_DEFAULT_AVAILABLE_SEARCH)
    readonly availableSearch?: string[];

    @Expose()
    @PaginationDefaultPage()
    readonly page: number;

    @Expose()
    @PaginationDefaultPerPage()
    readonly perPage: number;

    @Expose()
    @PaginationDefaultSort(USER_DEFAULT_SORT, USER_DEFAULT_AVAILABLE_SORT)
    readonly sort?: IPaginationSort;

    @Expose()
    @PaginationDefaultAvailableSort(USER_DEFAULT_AVAILABLE_SORT)
    readonly availableSort?: string[];
}
```

Then, the controller will look like

```typescript
@ResponsePaging('user.list') // <--- Use Response Paging Decorator for pagination
@AuthAdminJwtGuard(ENUM_PERMISSIONS.USER_READ)
@Get('/list')
async list(
    @Query()
    { page, perPage, sort, search, availableSort, availableSearch }: UserListDto // <--- The output here
): Promise<IResponsePaging> { // <--- User IResponsePaging for pagination
    const skip: number = await this.paginationService.skip(page, perPage);
    const find: Record<string, any> = {};

    if (search) {
        find['$or'] = [
            {
                firstName: {
                    $regex: new RegExp(search),
                    $options: 'i',
                },
                lastName: {
                    $regex: new RegExp(search),
                    $options: 'i',
                },
                email: {
                    $regex: new RegExp(search),
                    $options: 'i',
                },
                mobileNumber: search,
            },
        ];
    }
    const users: IUserDocument[] = await this.userService.findAll(find, {
        limit: perPage,
        skip: skip,
        sort: sort,
    });
    const totalData: number = await this.userService.getTotal(find);
    const totalPage: number = await this.paginationService.totalPage(
        totalData,
        perPage
    );

    const data: UserListSerialization[] =
        await this.userService.serializationList(users);

    return {
        totalData,
        totalPage,
        currentPage: page,
        perPage,
        availableSearch,
        availableSort,
        data,
    };
}
```

## PaginationListAbstract

```typescript
export abstract class PaginationListAbstract {
    abstract search?: string;
    abstract availableSearch?: string[];
    abstract page: number;
    abstract perPage: number;
    abstract sort?: IPaginationSort;
    abstract availableSort?: string[];
}
```

&nbsp;
