# Investigate Wings::ModelRegistry NameError Fix

**Status:** Not Started  
**Priority:** Medium  
**Source:** WVU Knapsack soft launch (identified 2026-08-10)

## Problem
Hyrax::Goddess::Query#model_class_for throws `NameError: uninitialized constant Wings::ModelRegistry` during work show page rendering when Wings::ModelRegistry is not defined.

## Current Workaround in Knapsack
Location: `config/initializers/goddess_query_fix.rb`
```ruby
if defined?(Hyrax::Goddess::Query)
  Hyrax::Goddess::Query.class_eval do
    def model_class_for(model)
      internal_resource = model.respond_to?(:internal_resource) ? model.internal_resource : nil
      return internal_resource.safe_constantize if internal_resource&.safe_constantize
      
      if defined?(Wings::ModelRegistry)
        Wings::ModelRegistry.lookup(model)
      else
        model.is_a?(Class) ? model : model.class
      end
    end
  end
end
```

## Tasks
- [ ] Check if Hyku main has already fixed this (PR search)
- [ ] If not fixed, investigate root cause in Wings or Hyrax
- [ ] Determine if fix should go to Hyku, Hyrax, or Wings
- [ ] Prepare upstream PR if fix is applicable

## Impact
- Affects all Hyku instances with conditional Wings::ModelRegistry availability
- Defensive check prevents NameError on initialization
- Small defensive patch suitable for upstream contribution

## Notes
- Used in production (hykudev) successfully
- Minimal change, backwards compatible
- Consider for Hyku 7.1.x patch release
