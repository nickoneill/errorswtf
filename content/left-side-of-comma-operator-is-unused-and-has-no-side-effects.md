---
title: "React: Left side of comma operator is unused and has no side effects"
description: "The render function of a React component expects a single element to be returned here but you're passing two."
date: 2018-11-24T17:00:14-08:00
---

## The Problem

You tried to expand the content in your component by adding an element before or after an element you were already rendering in this component.

{{< highlight jsx  >}}
render() {
  return (
    <h1>Your first element</h1>
    <h2>Your second element</h2>
  );
}
{{< / highlight >}}

The `render` function of a React component expects a single element to be returned here but you're passing two, the `h1` and `h2`.


## The Solution

If you're using a modern version of React, you can most likely wrap both of these elements in a `Fragment` that will virtually group them into a single element for a return, but won't render any additional content in your HTML.

{{< highlight jsx "hl_lines=3 6" >}}
render() {
  return (
  	<>
      <h1>Your first element</h1>
      <h2>Your second element</h2>
    </>
  );
}
{{< / highlight >}}

In React versions prior to 16, you can wrap these in an empty div or span which, while it will render in your HTML, most likely won't produce any additional side effects.

{{< highlight jsx "hl_lines=3 6" >}}
render() {
  return (
  	<div>
      <h1>Your first element</h1>
      <h2>Your second element</h2>
    </div>
  );
}
{{< / highlight >}}

Or sometimes it's a good idea to break one of these elements into its own component, further reducing the amount of complexity in a single place.
