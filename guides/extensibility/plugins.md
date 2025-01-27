---
shorty: Plugins
synopsis: >
    Learn how to extend the CAP framework with generic handlers and custom annotations, making it 
breadcrumbs:
  - Cookbook
  - Extensibility
  - Plugins
status: released
---

# Plugins

We understand many things as a CAP plugin.
It could provide a common cds model (entities, aspects, services, types...).
It could contain custom code by registering handlers.
It could offer support for additional cds annotations.
It could add build tasks.



## Providing a Reuse Model {.node}



## Providing a Reuse Model {.java}



## Generic Handlers {.node}


## Generic Handlers {.java}


## Custom Annotations

While CAP offers many annotations and existing CAP Plugins offer even more, you can also define and handle your own custom annotations.

```cds
entity Books : cuid {
  title : String @my.emoji: "🎉";
}
```

To do so, read annotations from the model in your generic handler.

```java
@ServiceName("*")
public class EmojiHandler implements EventHandler {
    @After
    public void decorateEmoji(CdsReadEventContext context) {
        CdsEntity entity = context.getTarget();
        // Inspect model for elements with @emoji annotation
        List<CdsElement> elements = entity.elements()
                .filter(e -> e.findAnnotation("@my.emoji").isPresent())
                .toList();

        // Modify all found elements
        context.getResult().forEach(result -> elements.forEach(element -> {
            String key = element.getName();
            String value = element.getAnnotationValue("@my.emoji", "🎉");
            result.put(key, result.get(key) + " " + value);
        }));
    }
}
```

You can also use the [data processor](../../java/cds-data#cds-data-processor) api to handle the iteration for you.
- learn more -> see the full example blog about emoji annotation handler

### Naming Conventions

Similar to [domain modeling](../domain-modeling#naming-conventions), we recommend the following simple naming conventions.

::: tip Use *Prefixes*

- Use mini-prefixes a la `cds` &rarr; for example, `@cds.autoexpose` or `@metering.count`
- Group your annotations semantically &rarr; for example, `@cds.on.insert` and `@cds.on.update`
- Direct annotations without prefix are reserved &rarr; for example, `@mandatory`

:::


::: tip Prefer *Lowercase*

- Start annotations and prefixes with a lowercase letter &rarr; for example, `@odata.etag`
- Use original naming convention if common vocabulary already exists &rarr; for example, `@PersonalData.DataSubject` for [OData](../../advanced/odata#vocabularies)

:::


::: tip Prefer *Concise* Names

- Don't repeat contexts &rarr; for example, `@metering.count` instead of `@metering.meteringCount`
- Prefer one-word names &rarr;  for example, `@metering.count` instead of `metering.countMetric`

:::


## Best Practices

- avoid allowing both a value and a map for an annotation: `@metered` and `@metered: { dimensions: { tenant: 'tenant_ID' } }`
