---
layout:     post
title:      Angular directives for conditional required
date:       2016-01-31
summary:    We are building a series of custom directives that do more than just `ng-required`
categories: tech angular
tags:      angular
---

### The Problem

`Angular` does support the concept of conditional required through the built-in directive `ng-required`. And it works great in simple scenarios. Consider the following example where `address2` is only required when `address1` is not empty.

{% highlight html %}
Address1 : <input name="address1" type="text" ng-model="address1" />
Address2 : <input name="address2" type="text" ng-model="address2" ng-required="!address1" />
{% endhighlight %}

This can be done using `ng-required` as shown above. But what if we also want `address2` to be 'not required' when `address1` is empty? There's no such thing as `ng-not-required`. If we take a look at the [source code of `ng-required`](https://github.com/angular/angular.js/blob/master/src/ng/directive/validators.js#L2), we can see that it is considered to be as valid **only** when the `ngModel` is **NOT** empty:

{%highlight javascript %}

var requiredDirective = function() {
  return {
    restrict: 'A',
    require: '?ngModel',
    link: function(scope, elm, attr, ctrl) {
      if (!ctrl) return;
      attr.required = true;

      ctrl.$validators.required = function(modelValue, viewValue) {
        return !attr.required || !ctrl.$isEmpty(viewValue);
      };

      attr.$observe('required', function() {
        ctrl.$validate();
      });
    }
  };
};
{% endhighlight %}

There's nothing wrong with `ng-required` being implemented this way, as its name implies, it is used for adding `required` restriction/validation on a form element conditionally. But it does become limited or useless when we have more complex problems to solve. For example:

1. Conditional **not** required is not supported as we have already talked about above.
2. Support for requireness of one or more elements in a group is very limited.

### The Solution

#### Create `not-required` directive

We can fix problem #1 with a custom directive that is slightly modified from `ng-required`, we call `demo-not-required`:

{%highlight javascript %}

app.directive('demoNotRequired', function($parse) {
  return {
    restrict: 'A',
    require: '?ngModel',
    link: function(scope, elm, attr, ctrl) {
      if (!ctrl) return;
      
      scope.$watch(attr.demoNotRequired, function() {
       ctrl.$validate();
      })
      ctrl.$validators.notRequired = function(modelValue, viewValue) {
        var isNotRequired = $parse(attr.demoNotRequired)(scope);
        return !isNotRequired || ctrl.$isEmpty(viewValue);
      };

    }
  };
});
{% endhighlight %}

As you can see, as opposite to `ng-required`, `demo-not-required` considers a form element to be valid when its ngModel **IS** empty rather than not.

#### Requireness of one or more elements in a group

With `ng-required` and `demo-not-required` in hand as building blocks, we can solve more complex problems.

##### At Least One Required

This is one of the common requirements in form validations. Among multiple form elements, at least one is required. In other words, it is ok to have more than one elements being non-empty, but it is not ok to all the elements being empty. 

For example, we may want 'phone number' and 'email' to be 'at least one required'. And this can easily be done using `ng-required`:

{% highlight html %}

<input name="phone" type="text" ng-model="phone" ng-required="!email"/>
<input name="email" type="email" ng-model="email" ng-required="!phone"/>
{% endhighlight %}

##### At Most One Required

This is kind of the opposite to 'at least one required'. This is to say that among multiple form elements, at most one is required. In other words, it is fairly ok to have all the elements left blank, but if we were to put values, no more than one element can have values. 

For example, some restaurant offers free salad or soup when you order any meal. And you can choose not to have either of them. This relationship can be implemented using `demo-not-required` we created above:


{% highlight html %}

<input name="salad" type="text" ng-model="salad" demo-not-required="!!soup" />
<input name="soup" type="text" ng-model="!!salad" />
{% endhighlight %}

##### Only One Required

This relationship is kind of a combination of 'at most one required' and 'at least one required' and can indeed be built as such. Take online payment as an example. Let's say you must leave either your credit card or debit card number as a method of payment:

{% highlight html %}
<input name="credit" type="text" ng-model="credit" ng-required="!debit" demo-not-required="!!debit" />
<input name="debit" type="text" ng-model="debit" ng-required="!credit" demo-not-required="!!credit" />
{% endhighlight %}

Above implementation looks ok but rather redundant. Since the logic in this case is a combination of the two, we can create a new directive 'demo-one-required' that combines the logic of the two existing directives:

{% highlight javascript %}
app.directive('demoOneRequired', function($parse) {
  return {
    restrict: 'A',
    require: '?ngModel',
    link: function(scope, elm, attr, ctrl) {
      if (!ctrl) return;
      
      scope.$watch(attr.demoOneRequired, function() {
       ctrl.$validate();
      })
      ctrl.$validators.oneRequired = function(modelValue, viewValue) {
        var isRequired = $parse(attr.demoOneRequired)(scope);
        return isRequired ? !ctrl.$isEmpty(viewValue) : ctrl.$isEmpty(viewValue);
      };
    }
  };
});
{% endhighlight %}

through which, the solution becomes clearer:

{% highlight html %}
<input name="credit" type="text" ng-model="credit" demo-one-required="!debit" />
<input name="debit" type="text" ng-model="debit" demo-one-required="!credit" />
{% endhighlight %}

### Refactoring
At this point, the directives we have built, namely: `demo-not-required`, `demo-one-required` all look similar. In fact, they only differ in the validation logic. What we can do is to build a general purpose conditional requireness validation directive that support all group and non-group relationship, including all the scenarios we have talked about. And the actual 'rule' that determines the group/non-group relationship is configurable. 

For example, let's say this directive we are going to build is called `demo-required`:

The controller will look like this:

{% highlight javascript %}
app.controller('DemoCtrl', function($scope) {
  $scope.validationConfig = {
    requiredGroup1: {
      ruleKey: 'ONLY_ONE',
      message: 'Please choose one from Field A, Field B etc.'
    }
  };
});
{% endhighlight %}

The configurable rules:

{% highlight javascript %}
var rulesConfig = {
  ONLY_ONE: function(thatIsEmpty, thisIsEmpty) {
    return thatIsEmpty ? thisIsEmpty : !thisIsEmpty;
  },
  AT_MOST_ONE: function(thatIsEmpty, thisIsEmpty) {
    return thatIsEmpty || !thisIsEmpty;
  },
  AT_LEAST_ONE: function(thatIsEmpty, thisIsEmpty) {
    return !thatIsEmpty || thisIsEmpty;
  }
};
{% endhighlight %}

Then finally in the html, we simply specify the groupId for `demo-required`:

{% highlight html %}
<input type="text" name="field1" ng-model="field1" demo-required="requiredGroup1" />
<input type="text" name="field2" ng-model="field2" demo-required="requiredGroup1" />

{% endhighlight %}

### The code
The directives we have built can be [found in plunker](http://plnkr.co/edit/kl2VlUls9jBFVbFJmSDn).
The final `demo-required` is still a work in progress, and can also be [found in plunker](http://plnkr.co/edit/DDqu0NPivBNsfuvIkyjr).

