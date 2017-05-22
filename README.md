# React Slot

Slot-based content distribution component for React. The technique was highly
influenced by the content distribution techniques used by
[Vuejs](http://vuejs.org/v2/guide/components.html#Content-Distribution-with-Slots).

## Install

    npm install react@">=15" react-dom@">=15" react-slot -S

## Quick Start

    // LayoutDefault.js
    // Generates a slotted HTML layout like the following:
    // <div class="layout-default">
    //  <header class="slot-header">Welcome!</header>
    //  <div class="slot-default main" role="main"></div>
    //  <div class="slot-info">Copyright 2017</div>
    //  <footer class="slot-footer"></footer>
    // </div>
    import * as React from 'react'
    import { Layout, Slot } from 'react-layout'

    export default function LayoutDefault (props) {
      const { children } = props
      return (
        <div className='layout-default'>
          <Slot name='header' as='header' content={children}>Welcome!</Slot>
          <Slot className='main' role='main' content={children} />
          <Slot name='info' content={children}>Copyright 2017</Slot>
          <Slot name='footer' as='footer' content={children} />
        </Layout>
      )
    }

    // PageHome.js
    // Create a page that will insert content into a layout's slots.
    import * as React from 'react'

    export default PageHome extends React.Component {
      static propsTypes = {
        layout: PropTypes.func.isRequired
      }

      constructor (props) {
        super(props)
        this.clicked = this.clicked.bind(this)
      }

      render () {
        const { layout } = this.props

        return (
          <layout>
            <div slot='header'><h1>Home</h1></div>
            <div slot>
              The main content
            </div>
          </layout>
        )
      }

      clicked (event) {
        console.log('clicked!')
      }
    }

    // App.js
    import * as React from 'react'
    import * as ReactDOM from 'react-dom'
    import PageHome from './PageHome'

    const page = <PageHome layout={DefaultLayout} />

    ReactDOM.render(
      page,
      document.getElementById('app')
    )

## API

### Slot

Slot is a component that is meant to compose your layouts with. These act
as the points where a layout can be altered by a parent using the layout
component.

**Props**

- `content` *[required]* The React children of the parent component
- `name` The name of this slot (inserted as class name 'slot-${name}')
- `id` The HTML id
- `className` Additional class names
- `dataset` *[default: {}]* An object with keys to set as 'data-' attributes
- `role` The HTML role
- `as` *[default: 'div']* The type of React element to create the root element as

Example:

    // A layout that will render HTML like the following:
    // <div class="layout-default">
    //  <header>Welcome!</header>
    //  <div class="slot-default main" role="main"></div>
    //  <div class="slot-info">Copyright 2017</div>
    //  <footer class="slot-footer"></footer>
    // </div>
    const LayoutDefault = props => {
      const { children } = props
      return (
        <div className='layout-default'>
          <Slot name='header' as='header' content={children}>Welcome!</Slot>
          <Slot className='main' role='main' content={children} />
          <Slot name='info' content={children}>Copyright 2017</Slot>
          <Slot name='footer' as='footer' content={children} />
        </Layout>
      )
    }

To insert content into a `Slot` the parent component using the layout needs to
designate React subtrees to use a slot by setting the `slot` prop on an element
to have its children inserted into the slot with the mathcing name (if one
exists).

    <div slot='slot-name'>...inserted into the slot-name slot...</div>

Any React elements with a `slot` set to `"default"`, or `true` will have thier
children inserted into the default slot (if one exists).

    <div slot>...inserted into the default slot...</div>
    <div slot='default'>...inserted into the default slot...</div>

Since only the children of a react subtree are inserted into a slot, all props
on the subtree root node are merged with the props defined on the `<Slot>`
element in the layout.

    // in the layout...
    <Slot name='footer' className='footer' />

    // in the parent component...
    <Layout>
      <div slot='footer' className='my-footer'>The Footer</div>
    </Layout>

    // Results in the footer slot being rendered as...
    <div class="slot-footer footer my-footer">TheFooter</div>

Also, if a default slot exists and no slotted subtree is found with the value
set to `"default"` or `true`, then all React nodes without a slot designation
will be inserted into the default slot.

    <div slot='slot-name'>...</div>
    <div>Default content1</div>
    <div>More default content</div>
    <div slot='slot-name2'>...</div>

Example Usage of `LayoutDefault`:

    // A page component that uses LayoutDefault to structure its content. This
    // will render a page that looks like this:
    // <div class="layout-default">
    //  <header>Welcome!</header>
    //  <div class="slot-default main" role="main">
    //    <div>Hello World!</div>
    //    <p>This is some more content inserted into the default slot</p>
    //  </div>
    //  <div class="slot-info">Copyright 2018</div>
    // </div>
    const Page = props => {
      return (
        <LayoutDefault>
          <div slot='info'>Copyright 2018</div>
          <div>Hello World!</div>
          <p>This is some more content inserted into the default slot</p>
        </LayoutDefault>
      )
    }

### slot(name, children)

This function will pull out the children of any React subtree designated by the
`slot` prop that matches the `name` argument. This function will not render a
root node at all, this is left up to the author to provide. This gives you more
control over a slot's root element. This is typically used inside another `<Slot>`
so parent components can choose to override the entire wrapping slot or the
inner slot.

    <Slot name='footer' content={children}>
      Copyright {slot('copyrightYear', children) || '2017'}
    </Slot>

Then in the parent component:

    // Replace the entire footer...
    <div slot='footer'>...</div>

    // ...or just replace the copyright year
    <div slot='copyrightYear'>2018</div>


## Related Modules

- [react-layout](https://npmjs.org/react-layout)
