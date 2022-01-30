# complicated raw json struct transformation process
 
## description
the targeted json is an embedded struct json. the objective of the transformation process is to flatten the struct, make it ready for the downstreaming realatinal dabase sql query.
the struct of the json is shown as below:
```
{cm: 
 {
	ln: string,
	sv: string,
	os: string,
	g: string,
	mid: string,
	nw: string,
	l: string,
	vc: string,
	hw: string
 },	
 ap: string,
 et:
 	[
 		ett: string,
 		en: string,
 		kv:
 		{
 			goodsid: string,
 			action: string,
 			extend1: string,
 			place: string,
 			category: string,
 			entry: string
 		}
 	]
}
```


