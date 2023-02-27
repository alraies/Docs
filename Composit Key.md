
# Create Entities with Composite Keys

Some entities may need to have composite keys. In that case, you can derive your entity from the non-generic Entity class. Example:


```c#
public class EmployeePosition : FullAuditedAggregateRoot
    {
        public int PositionId { get; set; }
        public int EmployeeEntityId { get; set; }
        public DateTime StartDate { get; set; }
        public DateTime? EndDate { get; set; }
        public bool IsCurrent { get; set; }
        public string Note { get; set; }
        public override object[] GetKeys()
        {
            return new object[] { PositionId, EmployeeEntityId };
        }
    }
```


Entities with composite keys should implement the GetKeys() method as shown above.

```
Composite primary keys has a restriction with repositories. Since it has not known Id property, 
you can not use IRepository<TEntity, TKey> for these entities. However, you can always use IRepository<TEntity>
```


## In Application Contracts create new Dto :

```
public class EmployeePositionKey
    {
        public int PositionId { get; set; }

        public int EmployeeEntityId { get; set; }
    }
```   

## In App Service you should Override ById Func.:

* use ```AbstractKeyCrudAppService``` 

```
public class EmployeePositionAppService : AbstractKeyCrudAppService<EmployeePosition, EmployeePositionDto, EmployeePositionKey, EmployeePositionGetListInput, EmployeePositionCreateUpdateDto, EmployeePositionCreateUpdateDto>,
    IEmployeePositionAppService
 ```

## Override

```
 protected override Task DeleteByIdAsync(EmployeePositionKey id)
    {
        return _repository.DeleteAsync(e =>
            e.PositionId == id.PositionId &&
            e.EmployeeEntityId == id.EmployeeEntityId
        );
    }

    protected override async Task<EmployeePosition> GetEntityByIdAsync(EmployeePositionKey id)
    {
        return await AsyncExecuter.FirstOrDefaultAsync(
            (await _repository.WithDetailsAsync()).Where(e =>
                e.PositionId == id.PositionId &&
                e.EmployeeEntityId == id.EmployeeEntityId
            ));
    }

    protected override IQueryable<EmployeePosition> ApplyDefaultSorting(IQueryable<EmployeePosition> query)
    {
        return query.OrderBy(e => e.PositionId);
    }

```