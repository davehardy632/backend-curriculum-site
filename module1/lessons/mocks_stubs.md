---
layout: page
title: Mocks and Stubs
---

## Learning Goals

* Understand what mocking and stubbing is and why we would use it.

## Vocabulary

* Mock
* Stub

## Warmup

With your partner, take 3 minutes to google an ‘action’ actor or movie you like, and find a picture of the actor(s) and their stunt double(s). Answer these questions…
- What do they have in common?
- What is different about their jobs?
- Why does the production company hire this double? How does it impact the actors job?
- When you watch a movie, it is usually obvious that a different human is acting a scene out? Does it change your movie-watching experience? Explain.


## Paired Exercise

### Step 1: Setup

To get access to methods that create mocks and stubs, we'll need to install and require the `mocha` gem. A gem is a package of code that someone else wrote. We bring them in to projects to make our lives easier!

```bash
gem install mocha
```

Once that's set, create a `bob_ross` directory with `lib` and `test` sub-directories. Create a `bob_test.rb` file in your `test` directory with the following code:

```ruby
require 'minitest/autorun'
require 'minitest/pride'
require 'mocha/minitest'
require './lib/bob'

class BobTest < Minitest::Test
  def test_it_exists
    bob = Bob.new
    assert_instance_of Bob, bob
  end

  def test_it_starts_with_no_paints
    bob = Bob.new
    assert_equal [], bob.paints
  end
end
```

Note that we have required `mocha/minitest` at the top of the file.

**Pair Work:**

Work with your partner to make the first tests pass. **You should not create a Paint class at any point during this lesson**.

### Step 2: Mocks

Let's imagine we wanted to test `Bob`'s `paints` method to see that it returns a collection of `Paint` instances. We might write a test like the following.

```ruby
def test_it_can_have_paint
  bob = Bob.new
  paint_1 = Paint.new("Alizarin Crimson")
  paint_2 = Paint.new("Van Dyke Brown")

  bob.add_paint(paint_1)
  bob.add_paint(paint_2)

  assert_equal [paint_1, paint_2], bob.paints
end
```

**Turn and Talk:**

What would we have to do to make this test pass?

In this particular example, we could go work to make the Paint class to move this test forward. That wouldn't be too bad because this example is pretty short. Imagine if the Paint class needed to hit an API in order to retrieve its color. Then we would have to implement all of that functionality before we could move forward with the Bob class. We might have a case where our teammate is already working on the Paint class and we don't want to duplicate work. We may also want to isolate this test from that particular interaction so that if a test breaks it is easier to identify what exactly has broken.

**Mocks are objects that stand in for other objects.** The other object might be one that's not implemented yet, doesn't yet have the functionality we need, or maybe we just want to work with a simpler situation. You can think of a mock as fake or a dummy object.

In the test above, we would have to create a Paint class in order to make this test pass. Instead, we are going to use a Mock object to stand in for a Paint object.

```ruby
paint_1 = mock("paint")
```

The argument to the `mock` method is an identifier. You can leave it out:

```ruby
paint_2 = mock
```

Remember, a mock is a simple object that stands in for another object. At the base level, a mock is just a "thing" -- a blank canvas that we can use for just about anything.

**Pair Work:**

Update this test so that it uses Mocks instead of Paints. Make the test pass.

### Step 3: Stubs

Let's add another test:

```ruby
def test_it_can_return_colors
  bob = Bob.new
  paint_1 = mock("paint 1")
  paint_2 = mock("paint 2")
  bob.add_paint(paint_1)
  bob.add_paint(paint_2)

  assert_equal ["Alizarin Crimson", "Van Dyke Brown"], bob.paint_colors
end
```

**A stub is a fake method.** It can be added to an object that doesn't have that method, or it can override an existing method. We can add a stub to a mock so our fake object will now have a fake method:

```ruby
paint_1 = mock
paint_1.stubs(:color).returns("Van Dyke Brown")
```

Now, whenever we call `paint_1.color` it will return `"Van Dyke Brown"`.


**Pair Work:**

Update this test so that it stubs out the color method for the Mock objects. Make the test pass.

### Step 4: Mock Expectations

Replace your existing `paint_colors` method with the following:

```ruby
def paint_colors
  ["Alizarin Crimson", "Van Dyke Brown"]
end
```

Run your test. What happens?

Mocks can do more than just stand there. **They can also verify that they have been called.** Why might we want that functionality when using stubs?

```ruby
paint_1 = mock
paint_1.expects(:color).returns("Van Dyke Brown")
```

Run your tests and you will notice they now fail. Read the failure carefully.

Change your `paint_colors` method to pass the test.

**Pair Work:**

Add the following test. Update it to use mocks and stubs so that you can make it pass without creating the Paint class. Then make the test pass:

```ruby
  def test_it_can_total_paint_amount
    bob = Bob.new
    paint_1 = Paint.new("Alizarin Crimson", 42)
    paint_2 = Paint.new("Van Dyke Brown", 25)

    bob.add_paint(paint_1)
    bob.add_paint(paint_2)

    assert_equal 67, bob.total_paint_amount
  end
```

### Interview Question

What are mocks and stubs? When have you used them?

## The Ultimate CFU

* Can you think of a Cross Check test you've already written that could use mocks and stubs instead?
* When would you use a stub over a mock with expectations and returns?

## Further Reading

- Martin Fowler - Test Double: link [here](http://www.martinfowler.com/bliki/TestDouble.html)
- Gerard Meszaros - Test Double: link [here](http://xunitpatterns.com/Test%20Double.html)
