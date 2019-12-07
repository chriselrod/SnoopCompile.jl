# NEWS.md

This file contains information about major updates since 1.0.

## Version 1.1

This version implements major changes in how `parcel` works on the output of `@snoopi`:

- keyword-supplying methods are now looked up as `Core.kwftype(f)` rather than by
  the gensymmed name (`var"#f##kw"` or `getfield(Base, Symbol("#kw##sum"))`)
- On Julia 1.4+, SnoopCompile will write precompile directives for keyword-body functions
  (sometimes called the "implementation" function) that look them up by an algorithm that
  doesn't depend on the gensymmed name. For example,
  ```julia
  let fbody = try __lookup_kwbody__(which(printstyled, (String,Vararg{String,N} where N,))) catch missing end
    if !ismissing(fbody)
        precompile(fbody, (Bool,Symbol,typeof(printstyled),String,Vararg{String,N} where N,))
    end
  end
  ```
  For files that have precompile statements for keyword-body functions, the `__lookup_kwbody__`
  method is defined at the top of the file generated by `SnoopCompile.write`.
- Function generators are looked up in a gensym-invariant manner with statements like
  ```julia
  precompile(Tuple{typeof(which(FuncKinds.gen2,(Int64,Any,)).generator.gen),Any,Any,Any})
  ```
- Anonymous functions and inner functions are looked up inside an `isdefined` check, to
  prevent errors when the precompile file is used on a different version of Julia with
  different gensym numbering.

Other changes:
- A convenience utility, `timesum`, was introduced (credit to aminya)