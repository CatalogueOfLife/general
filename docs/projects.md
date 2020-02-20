

# Projects

A project is a different term for a managed dataset, i.e. a dataset of origin 'MANAGED' that can be manually modified and assembled from sectors based on other datasets.

The Clearinghouse is designed to be a public place that allows anyone to look at datasets. Making the assembly of the CoL more transparent also means exposing sectors and decisions to the public, i.e. an anonymous user.

# Conclusions

## Project context
A project specific view on datasets is often needed when working in the assembly tree or with decisions in the workbench.
Similar to the user login we should keep this as a client state or at least a query parameter, not as part of the URL path.
The current project scope (the dataset alias) should be shown above the menu or next to the user login. Changing a project scope should be an explicit action and the current project should be stored in the user settings so we can activate it automatically whenever the user logs in. Once [authorization is scoped to datasets](https://github.com/Sp2000/colplus-backend/issues/580) it makes sense to limit the available projects for a user to the datasets he is authorized to work on.

## Public datasets
Do not scope datasets under a project. All datasets should have a simple frontend & backend URL '/dataset/1234' that the public can view.

If possible it makes sense to expose all sectors and decisions for all projects under a dataset, but I fear with nunerous projects and releases this increases the complexity of a datase view a lot. We should definitely at least show a list of all projects that make use of a given dataset, i.e. that have at least one sector defined.

Projects must also be viewable by the public. We could restrict access to released versions, but it is probably benefitial to allow users to see the latest draft state. E.g. for GSDs to assess how their data is represented in the CoL before it is released.

## Releases
A project can release the current state into a new immutable dataset with a new id. Releasing copies the entire dataset with metadata, data and all sector definitions, decisions & estimates. Identifiers for names, taxa and references are assigned a stable id at this point only. This allows data in the draft to be frequently changed and most importantly records to be fully deleted without preserving their id, e.g. when sorting out duplicates.
Released datasets still have 'origin=MANAGED' but also a flag 'locked=true'. We should consider to use a new origin 'RELEASE for them with a new 'projectKey' property that points to the current draft so we can better group them.

Released datasets should probably not clutter the dataset search and other places within a single (source) dataset e.g. when listing all project sectors that make use of the same source dataset.

## Parameter names
All catalogue parameters should be renamed to project, i.e. in particular
 - sector
 - tbd ...

