## Consider the following for interfaces to use and or a mix of them:

- CreatedByUserId (nonullable)
- CreatedAtUtc (nonullable)
- UpdatedByUserId (nullable)
- UpdatedAtUtc (nullable)
- DeletedByUserId (nullable)
- DeletedAtUtc (nullable)
- IsDeleted (nonullable default false)

### With the following checks
 - Appropriate foreign key constraints
 - Timestamps not default values
 - Check constraint if IsDeleted must have who and when and the opposite
 - Cascading creator and when and modifier and when would probably be good to but maybe too much boiler plate.
 - They should ideally be at the end of the table but that's not always easy depending on the platform.
