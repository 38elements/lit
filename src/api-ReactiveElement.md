{ "header": {"name": "API", "order": 4}, "order": 1 }
---
# ReactiveElement

## class ReactiveElement

要素のプロパティや属性を取り扱う要素のベースクラスです。
プロパティが変更されると、updateメソッドが非同期で実行されます。
更新のレンダリング処理を変更したい場合はupdateメソッドをサブクラスで実装します。

### Import

```
import { ReactiveElement } from 'lit';
```

### Attributes

#### attributeChangedCallback(name: string, _old: null | string, value: null | string): void

要素の属性の値が変化した時、プロパティの値をそれと同期させます。
具体的に言うと要素の属性がセットされるとそれに対応するプロパティがセットされます。
このメソッドを実装することはほとんどありません。
このメソッドをオーバーライドする際は、オーバーライドしたメソッド内で`super.attributeChangedCallback(name, _old, value)`を実行する必要があります。

`attributeChangedCallback`に関する詳しい情報は[responding to attribute changes](https://developer.mozilla.org/en-US/docs/Web/API/Web_components/Using_custom_elements#responding_to_attribute_changes)を見てください。

#### static observedAttributes: Array<string>

登録されているプロパティに対応する属性のリストを返します。

### Controllers

#### addController(controller: [ReactiveController](https://lit.dev/docs/api/controllers/#ReactiveController)): void

[リアクティブコントローラ](https://japanese-document.github.io/lit/composition-controllers.html)を要素のリアクティブアップデートサイクルに組み込みます。
要素はライフサイクルコールバックに対応したコントローラのフックメソッドを自動的に実行します。
`addController()`が実行されたとき要素がdocumentに接続している場合、
即座にcontrollerの`hostConnected()`が実行されます。

#### removeController(controller: [ReactiveController](https://lit.dev/docs/api/controllers/#ReactiveController)): void

要素から指定したリアクティブコントローラを削除します。

### Dev mode

#### static disableWarning?: (warningKind: [WarningKind](https://lit.dev/docs/api/misc/#WarningKind)) => void

指定した警告カテゴリをクラスに対して無効化します。
このメソッドはdevelopmentビルドにのみ存在します。
たから、このメソッドを使用する時は下記のような予防策を講じると良いでしょう。

```
// ReactiveElementのすべてのサブクラスで無効にします。
ReactiveElement.disableWarning?.('migration');
// MyElementとそのサブクラスのみで無効にします。
MyElement.disableWarning?.('migration');
```

#### static enabledWarnings?: Array<[WarningKind](https://lit.dev/docs/api/misc#WarningKind)>

クラスで有効な警告カテゴリです。
このプロパティはdevelopmentビルドのみ使用することができます。

#### enableWarning?: (warningKind: [WarningKind](https://lit.dev/docs/api/misc#WarningKind)) => void

指定した警告カテゴリをクラスに適用して有効にします。
このメソッドはdevelopmentビルドにのみ存在します。
たから、このメソッドを使用する時は下記のような予防策を講じると良いでしょう。

```
// ReactiveElementのすべてのサブクラスで有効にします。
ReactiveElement.enableWarning?.('migration');
// MyElementとそのサブクラスのみで有効にします。
MyElement.enableWarning?.('migration');
```

### ライフサイクル

#### connectedCallback(): void

初回の接続時、要素のrenderRootを生成して、要素にスタイルを適用して、更新を有効にします。

#### disconnectedCallback(): void

Allows for `super.disconnectedCallback()` in extensions while reserving the possibility of making non-breaking feature additions when disconnecting at some point in the future.

### その他

#### static addInitializer(initializer: [Initializer](https://lit.dev/docs/api/misc/#Initializer)): void

これはReactiveElementのサブクラスで使います。例えば、下記のようにデコレータのようにReactiveControllerの設定をインスタンス毎に処理を行う必要がある場合に使います。

```ts
const myDecorator = (target: typeof ReactiveElement, key: string) => {
  target.addInitializer((instance: ReactiveElement) => {
    // これは要素の生成時に実行されます。
    new MyController(instance);
  });
}
```

上記のデコレータを下記のようにフィールドに付与するとインスタンス単位でコントローラを追加するイニシャライザを実行します。

```ts
class MyElement extends LitElement {
  @myDecorator foo;
}
```

イニシャライザ(Initializers)はコンストラクタ毎に設定されます。
サブクラスにイニシャライザを追加してもスーパークラスにイニシャライザは追加されません。
イニシャライザはコンストラクタで実行されます。だから、クラスの継承順で実行されます。つまり、スーパークラスからインスタンスのクラスの順番で実行されます。

#### static finalize(): boolean

Creates property accessors for registered properties, sets up element styling, and ensures any superclasses are also finalized. Returns true if the element was finalized.

#### static finalized: boolean

プロパティの生成が完了しているかどうかを示すフラグ

### プロパティ

#### static createProperty(name: PropertyKey, options?: [PropertyDeclaration](https://lit.dev/docs/api/ReactiveElement/#PropertyDeclaration)<unknown, unknown>): void

Creates a property accessor on the element prototype if one does not exist and stores a PropertyDeclaration for the property with the given options. The property setter calls the property's hasChanged property option or uses a strict identity check to determine whether or not to request an update.
This method may be overridden to customize properties; however, when doing so, it's important to call super.createProperty to ensure the property is setup correctly. This method calls getPropertyDescriptor internally to get a descriptor to install. To customize what properties do when they are get or set, override getPropertyDescriptor. To customize the options for a property, implement createProperty like this:

```
static createProperty(name, options) {
  options = Object.assign(options, {myOption: true});
  super.createProperty(name, options);
}
```

#### static elementProperties: PropertyDeclarationMap

Memoized list of all element properties, including any superclass properties. Created lazily on user subclasses when finalizing the class.

#### static getPropertyDescriptor(name: PropertyKey, key: string | symbol, options: [PropertyDeclaration](https://lit.dev/docs/api/ReactiveElement/#PropertyDeclaration)<unknown, unknown>): undefined | PropertyDescriptor

Returns a property descriptor to be defined on the given named property. If no descriptor is returned, the property will not become an accessor. 

```js
class MyElement extends LitElement {
  static getPropertyDescriptor(name, key, options) {
    const defaultDescriptor =
        super.getPropertyDescriptor(name, key, options);
    const setter = defaultDescriptor.set;
    return {
      get: defaultDescriptor.get,
      set(value) {
        setter.call(this, value);
        // custom action.
      },
      configurable: true,
      enumerable: true
    }
  }
}
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