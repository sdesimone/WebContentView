Purpose
--------------

WebContentView provides a simple native interface for rendering rich HTML content in an iPhone app using a UIWebView. It is not designed to work as a embedded web browser for on or offline content, it doesn't load URLs - think of it more as a "rich" version of the UITextView, where content can be set using HTML instead of plain text.


Installation
--------------

To use the WebContentView in your project, just drag the class files into your project. It has no dependencies.


Properties
---------------

Each WebContentView instance has the following properties:

	@property (nonatomic, retain, readonly) UIScrollView *scrollView;
	
This is the internal UIScrollView used for scrolling the content. A native scrollview is used instead of scrolling the UIWebView because this feels more like a normal native control, and gives you more precise control over the scrolling behaviour. You can register a delegate for this scroll view if you want as the delegate is not used by WebContentView.
	
	@property (nonatomic, retain, readonly) UIWebView *webView;
	
This is the UIWebView used internally by the WebContentView. Note that the WebContentView is registered as the UIWebView's delegate, so overriding that will break some of the standard WebContentView behaviours.
	
	@property (nonatomic, retain) IBOutlet UIView *header;
	
An optional UIView supplied as a header for the web content. This will automatically be placed above the web content within the scroll view and will scroll up and down with the web content.
	
	@property (nonatomic, retain) IBOutlet UIView *footer;
	
An optional UIView supplied as a footer for the web content. This will automatically be placed below the web content within the scroll view and will scroll up and down with the web content. If the web content is not tall enough to fill the view, the footer will be locked to the bottom of the WebContentView, with a gap below the web content.
	
	@property (nonatomic, copy) NSString *content;
	
The content to display. This should be provided as HTML. This can be an HTML fragment rather than a full HTML document (i.e. a `<head>` or `<body>` tag are not required). The content can contain embedded styles, but it's easier and more efficient to set the styles using the styles instance property or the defaultStyles class property. Any image references or other URLs in the content will be relative to the application bundle.

	@property (nonatomic, copy) NSString *styles;
	
The styles used by the HTML content for the WebContentView instance. These are appended to the shared defaultStyles stylesheet. It is more efficient for performance to set the styles initially before the content, however you can set the styles after the content has loaded and they will be rewritten using JavaScript, which is much quicker than reloading the content from scratch, and won't flicker. Any images or fonts referenced in the styles will be relative to the application bundle.
	
	@property (nonatomic, assign) BOOL scrollEnabled;
	
This enabled/disables scrolling on the internal UIScrollView of the WebContentView.
	
	@property (nonatomic, assign) id<WebContentViewDelegate> delegate;
	
This is the delegate for the WebContentView.
	
	@property (nonatomic, readonly, getter = isLoading) BOOL loading;

This property can be used to detect if the content has finished loading.


Methods
---------------

The WebContentView class has the following shared methods:

	+ (NSString *)defaultStyles;
	
This returns the shared default stylesheet used by all WebContentView instances.

	+ (NSString *)sharedStyles;
	
This returns a shared custom stylesheet used by all WebContentView instances.
	
	+ (void)setSharedStyles:(NSString *)styles;
	
This sets the shared stylesheet used by all WebContentView instances. Any existing instances will be refreshed with the new styles if this value is set.
	
	+ (void)preloadContent:(NSString *)content;
	
WebContentViews sometimes take a fraction of a second to render, which can result in a content flash when they are displayed. You can use this method to preload the content so that it will render instantly. Preloading effectively creates a WebContentView instance in the background, so it can consume a lot of memory. The number of preloaded views is capped to 10 by default (you can edit this constant in the .m file if you choose). In the event of a memory warning, the preloaded views will be cleared out, so it's a good idea to preload at the last possible opportunity before the WebContentView is displayed.
	
	+ (void)preloadContent:(NSString *)content withStyles:(NSString *)styles;

This works the same way as `preloadContent:`, except that you can also preload a particular set of styles with the view. Note that preloaded views will already use the shared styles, so unless you need specific styles to go with the content, you need't use this method.


Each WebContentView instance has the following methods:

	- (void)flashScrollIndicators;
	
This will flash the scroll bar if the contents are larger than the view bounds.
	
	- (void)sizeContentToFit;
	
This will automatically resize the WebContentView scrollable content region to match the size of the HTML content, and reposition the header and footer views. You shouldn't normally need to call this manually, but it may be required if the contents are resized due to CSS hover effects or JavaScript events do not trigger an automatic resize.
	

Delegate methods
---------------

	- (void)webContentViewDidFinishLoad:(WebContentView *)webContentView;
	
This method is called when a WebContentView finishes loading/displaying the content. Note that if the content is preloaded, this method will never be called.
	
	- (BOOL)webContentView:(WebContentView *)webContentView shouldOpenURL:(NSURL *)URL;
	
This method is called whenever the user taps a link in the WebContentView. By default, any WebContentView links will open in Safari, but you can override this behaviour and handle them yourself inside the app by returning NO.