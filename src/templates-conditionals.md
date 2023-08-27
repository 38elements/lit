{ "header": {"name": "テンプレート", "order": 1}, "order": 1 }
---
# 条件

Since Lit leverages normal Javascript expressions, you can use standard Javascript control flow constructs like [conditional operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator), function calls, and `if` or `switch` statements to render conditional content.

JavaScript conditionals also let you combine nested template expressions, and you can even store template results in variables to use elsewhere.

## Conditionals with the conditional (ternary) operator

Ternary expressions with the conditional operator, `?`, are a great way to add inline conditionals:

```ts
render() {
  return this.userName
    ? html`Welcome ${this.userName}`
    : html`Please log in <button>Login</button>`;
}
```

## Conditionals with if statements

You can express conditional logic with if statements outside of a template to compute values to use inside of the template:

```ts
render() {
  let message;
  if (this.userName) {
    message = html`Welcome ${this.userName}`;
  } else {
    message = html`Please log in <button>Login</button>`;
  }
  return html`<p class="message">${message}</p>`;
}
```

Alternately, you can factor logic into a separate function to simplify your template:

```ts
getUserMessage() {
  if (this.userName) {
    return html`Welcome ${this.userName}`;
  } else {
    return html`Please log in <button>Login</button>`;
  }
}
render() {
  return html`<p>${this.getUserMessage()}</p>`;
}
```

## Caching template results: the cache directive

In most cases, JavaScript conditionals are all you need for conditional templates. However, if you're switching between large, complicated templates, you might want to save the cost of recreating DOM on each switch.

In this case, you can use the `cache` _directive_. The cache directive caches DOM for templates that aren't being rendered currently.

```ts
render() {
  return html`${cache(this.userName ?
    html`Welcome ${this.userName}`:
    html`Please log in <button>Login</button>`)
  }`;
}
```

See the [cache directive](/docs/v2/templates/directives/#cache) for more information.

## Conditionally rendering nothing { #conditionally-rendering-nothing }

Sometimes, you may want to render nothing in one branch of a conditional operator. This is commonly needed for child expressions and also sometimes needed in attribute expressions.

For child expressions, the values `undefined`, `null`, the empty string (`''`), and Lit's [nothing](/docs/v2/api/templates/#nothing) sentinel value all render no nodes. See [Removing child content](/docs/v2/templates/expressions/#removing-child) for more information.

This example renders a value if it exists, and otherwise renders nothing:

```ts
render() {
  return html`<user-name>${this.userName ?? nothing}</user-name>`;
}
```

For attribute expressions, Lit's [nothing](/docs/v2/api/templates/#nothing) sentinel value removes the attribute. See [Removing an attribute](/docs/v2/templates/expressions/#removing-attribute) for more information.

This example conditionally renders the `aria-label` attribute:

```ts
html`<button aria-label="${this.ariaLabel || nothing}"></button>`
```

---

## License

### Japanese part

[Creative Commons Attribution-NonCommercial 4.0 International Public License](https://creativecommons.org/licenses/by-nc/4.0/legalcode)

Copyright (c) 2022 38elements

### Other

[Creative Commons Attribution 3.0 Unported](https://creativecommons.org/licenses/by/3.0/deed.en)

Copyright (c) 2020 Google LLC. All rights reserved.

BSD 3-Clause License

Copyright (c) 2020 Google LLC. All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its
   contributors may be used to endorse or promote products derived from
   this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.