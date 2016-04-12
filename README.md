# GraphiQL-Swift
A swift component with a DSL to declare GraphQL queries and to get string representations out of them
# Contents
- [Installation] (#installation)
- [Usage] (#usage)
- [Authors] (#authors)
- [License] (#license)

## Installation

## Usage
Let's assume, we have the id of an article and we want to have the `headline`, `body` text and `opener image` of that article.

Our graphQL query for that will look like this:

```graphQL
{
	test:content(id: 153082687){
		...contentFields
	}
}
fragment contentFields on Content {
	headline,
	body,
	image(role: opener){
		...imageContent
	}
}
fragment imageContent on Image {
	id,
	url
}
```

First, let's create a `Fragment` to fetch the contents of an image, namely the image `id` and the image `url`

```swift
let imageContent = Fragment(
	withAlias: "imageContent",
	name: "Image",
	fields: [
		"id",
		"url"
	]
)
```

Next, let's embed the `Fragment` into a `Request` that gets the opener image

```swift
let imageContentRequest = Request(
	name: "image",
	arguments: [
		Argument(key: "role", value: "opener")
	],
	fields: [
		imageContent
	]
)
```  

So now we have a Request with an embedded Fragment. Let's go one step further.  
If we want to, we can imbed that Request into another Fragment. (We can also embed Fragments into Fragments)  
Additionally to the opener image with its id and url we also want the `headline` and `body` text of the article.

```swift
let articleContent = Fragment(
	withAlias: "contentFields",
	name: "Content",
	fields: [
		"headline",
		"body",
		imageContentRequest
	]
)
```

Finally, we put everything together as a `Query`. A Query always has a top level Request to get everything started, and requires all the Fragments that are used inside.

```swift
let query = Query(withRequest: Request(
	withAlias: "test",
	name: "content",
	arguments: [
		Argument(key: "id", values: [153082687])
	],
	fields: [
		articleContent
	]),
	fragments: [articleContent, imageContent]
)
```  
All we have to do now is to call `create()` on our Query and we're good to go.

``` 
print(query.create())
```
## Authors
`GraphiQL-Swift` was made in-house by WeltN24
### Contributors
Felix Dietz, [felix.dietz@weltn24.de](mailto:felix.dietz@weltn24.de), [@joemcbomb](https://github.com/joemcbomb) on Github, [@joemcbomb](https://twitter.com/joemcbomb) on Twitter

Vittorio Monaco, [vittorio.monaco@weltn24.de](mailto:vittorio.monaco@weltn24.de), [@vittoriom](https://github.com/vittoriom) on Github, [@Vittorio_Monaco](https://twitter.com/Vittorio_Monaco) on Twitter

## License
`GraphiQL-Swift` is available under the MIT license. See the LICENSE files for more info.