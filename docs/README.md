# BBjInfiniteScroll Widget

<p>
  <a href="http://www.basis.com/downloads">
    <img src="https://img.shields.io/badge/BBj-v22.00-blue" alt="BBj v22.00" />
  </a>
  <a href="https://github.com/BBj-Plugins/BBjInfiniteScroll/blob/master/README.md">
    <img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="BBjInfiniteScroll is released under the MIT license." />
  </a>
  <a href="https://github.com/necolas/issue-guidelines/blob/master/CONTRIBUTING.md#pull-requests">
    <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg" alt="PRs welcome!" />
  </a>
   <a href="https://basishub.github.io/basis-next/#/dwc/bbj-infinite-scroll">
    <img src="https://img.shields.io/badge/Component-bbj--infinite--scroll-%23006aff" alt="Tag Name">
  </a>
</p>

BBjInfiniteScroll lets a user loads content continuously as the user scrolls down, eliminating the need for pagination.

?> **Note:** Infinite scrolling is a technique that loads more content as you scroll. It allows you to continue scrolling indefinitely and is sometimes known as endless scrolling. The technique is often used in social media feeds.

## Features

- Easy to set up
- Easy to customize
- Provide a paginater helper to paginate the data

And much more !

## Installation

- Clone the [project](https://github.com/BBj-Plugins/BBjInfiniteScroll) locally , then add `BBjInfiniteScroll` to your BBj paths
- Or [Use the plugins manager](https://www.bbj-plugins.com/en/get-started)

## The gist

```BBj
use ::BBjInfiniteScroll/BBjInfiniteScroll.bbj::BBjInfiniteScroll
use ::BBjInfiniteScroll/BBjInfiniteScroll.bbj::BBjInfiniteScrollEvent

wnd! = BBjAPI().openSysGui("X0").addWindow(10,10,300,400,"BBjInfiniteScroll")
wnd!.setCallback(BBjAPI.ON_CLOSE,"eoj")

infiniteScroll! = new BBjInfiniteScroll(wnd!)
infiniteScroll!.onScroll("onScroll")

lastIndex! = 0

process_events

onScroll:
  declare auto BBjInfiniteScrollEvent payload!

  ev! = BBjAPI().getLastEvent()
  payload! = ev!.getObject()
  control! = payload!.getControl()
  canvas! = control!.getCanvas()
  startIndex! =  lastIndex!
  endIndex! = lastIndex! + 20

  for i = startIndex! to endIndex! 
    container! = canvas!.addChildWindow(canvas!.getAvailableControlID(), 0, 0, 0, 0, "", $00108800$, BBjAPI().getSysGui().getAvailableContext())
    container!.setStyle("padding", "var(--bbj-space-xs) var(--bbj-space-s)")
    container!.setAttribute("data-index", str(i))
    container!.addStaticText(canvas!.getAvailableControlID(),0,0,0,0, str(i) + " - Static Text" ,$0000$)

    lastIndex! = lastIndex! + 1
  next 

  control!.update()
return

eoj:
release
```

<br><br>
<div style="text-align: center;">
  <img style="border:thin solid var(--bbj-color-default);" src="assets/gist.gif" alt="BBjInfiniteScroll gist">
</div>
<br><br>

## Texts And Icons

You can configure the loading text and loading icon of the widget using 
the following methods:

- BBjInfiniteScroll.setText()
- BBjInfiniteScroll.setIcon()

Icons are strings which can be in any of the following formats:

- **Url**: (ex: /path/to/image.gif)
- **Data Url**: (ex: data:image/jpeg;base64,/9j/4SDpRXhpZgAAT....)
- **icon**: An icon to load from the default BBj icons pool. (ex: `animated-spinner`)
- **pool:icon**: An icon to load from the passed pool. (ex: `feather:load`)

```BBj
infiniteScroll! = new BBjInfiniteScroll(wnd!)
infiniteScroll!.setText("Wait a moment...")
infiniteScroll!.setIcon("https://cdnjs.cloudflare.com/ajax/libs/timelinejs/2.25/css/loading.gif")
```

<br><br>
<div style="text-align: center;">
  <img style="border:thin solid var(--bbj-color-default);" src="assets/text-and-icons.gif" alt="BBjInfiniteScroll text and icons">
</div>
<br><br>

## Events

BBjInfiniteScroll exposes only one event `onScroll`. You can listen to the `onScroll` event by setting a callback using the `BBjInfiniteScroll:onScroll` method. The event's payload is an instance of `BBjInfiniteScrollEvent`

## Pagination 

BBjInfiniteScroll ships a helper class `BBjInfiniteScrollPaginater` which can help you paginate your data. 

With `BBjInfiniteScrollPaginater` you set the total amount of the items
and number of items per page , then the paginater provides you with the:

- **StartIndex**: The index of the first item
- **EndIndex**: The index of the last item
- **TotalPages** : The number of available pages

You can navigate throw pages by setting the currentPage with the method `BBjInfiniteScrollPaginater.setCurrentPage`


?> **Note:** In the following sample, notice how we are using the `setCompleted` method to mark the end of infinite scrolling. Invoking this method won't update the canvas automatically , you still need to invoke `update` to update the canvas.

```BBj
use ::BBjInfiniteScroll/BBjInfiniteScroll.bbj::BBjInfiniteScroll
use ::BBjInfiniteScroll/BBjInfiniteScroll.bbj::BBjInfiniteScrollPaginater

wnd! = BBjAPI().openSysGui("X0").addWindow(10,10,300,400,"BBjInfiniteScroll")
wnd!.setCallback(BBjAPI.ON_CLOSE,"eoj")

infiniteScroll! = new BBjInfiniteScroll(wnd!)
infiniteScroll!.onScroll("onScroll")

paginater! = new BBjInfiniteScrollPaginater()
paginater!.setTotalItems(60)
paginater!.setPageSize(20)

canvas! = infiniteScroll!.getCanvas()

process_events

onScroll:
  startIndex! = paginater!.getStartIndex()
  endIndex! = paginater!.getEndIndex()

  if(paginater!.getEndIndex() > -1)
    for i = startIndex! to endIndex!
      container! = canvas!.addChildWindow(canvas!.getAvailableControlID(), 0, 0, 0, 0, "", $00108800$, BBjAPI().getSysGui().getAvailableContext())
      container!.setStyle("padding", "var(--bbj-space-xs) var(--bbj-space-s)")
      container!.setAttribute("data-index", str(i))
      container!.addStaticText(canvas!.getAvailableControlID(),0,0,0,0, str(i) + " - Static Text" ,$0000$)
    next i

    currentPage! = paginater!.getCurrentPage()
    paginater!.setCurrentPage(currentPage! + 1)
  fi

  if(endIndex! < 0 or endIndex! = (paginater!.getTotalItems() -1))
    infiniteScroll!.setCompleted(1)
  fi

  infiniteScroll!.update()
return

eoj:
release
```

<br><br>
<div style="text-align: center;">
  <img style="border:thin solid var(--bbj-color-default);" src="assets/paginater.gif" alt="BBjInfiniteScrollPaginater">
</div>
<br><br>

## Pull To Refresh

The [BBjPullToRefresh](https://bbj-plugins.github.io/BBjPullToRefresh/#/) widget can be integrated easily with the `BBjInfiniteScroll` widget.  The `BBjPullToRefresh` must be registered on the BBjInfiniteScroll's canvas. 

?> **Note:** In the following sample, Notice how we are configuring the 
`BBjPullToRefresh` to be attached to the BBjInfiniteScroll's canvas itself instead of the canvas's parent. This give's the user the feeling that list is being pulled instead of the list's window


```BBj
use ::BBjInfiniteScroll/BBjInfiniteScroll.bbj::BBjInfiniteScroll
use ::BBjPullToRefresh/BBjPullToRefresh.bbj::BBjPullToRefresh

wnd! = BBjAPI().openSysGui("X0").addWindow(10,10,300,400,"BBjInfiniteScroll")
wnd!.setCallback(BBjAPI.ON_CLOSE,"eoj")

infiniteScroll! = new BBjInfiniteScroll(wnd!)
infiniteScroll!.onScroll("onScroll")

pullToRefresh! = new BBjPullToRefresh(infiniteScroll!.getCanvas(), BBjPullToRefresh.PREPEND_SELF)
pullToRefresh!.onRefresh("onRefresh")

lastIndex! = 0

process_events

onScroll:
  canvas! = infiniteScroll!.getCanvas()
  startIndex! =  lastIndex!
  endIndex! = lastIndex! + 20

  for i = startIndex! to endIndex! 
    container! = canvas!.addChildWindow(canvas!.getAvailableControlID(), 0, 0, 0, 0, "", $00108800$, BBjAPI().getSysGui().getAvailableContext())
    container!.setStyle("padding", "var(--bbj-space-xs) var(--bbj-space-s)")
    container!.setAttribute("data-index", str(i))
    container!.addStaticText(canvas!.getAvailableControlID(),0,0,0,0, str(i) + " - Static Text" ,$0000$)
    lastIndex! = lastIndex! + 1
  next 

  infiniteScroll!.update()
return

onRefresh:
  lastIndex! = 0

  infiniteScroll!.setCompleted(1)
  infiniteScroll!.update()

  controls! = infiniteScroll!.getCanvas().getAllControls()
  size! = controls!.size() - 1
  for i = 0 to size!
    control! = controls!.get(i)

    if(control!.getID() <> pullToRefresh!.getID())
      control!.destroy()
    fi
  next i

  pullToRefresh!.finish()

  infiniteScroll!.setCompleted(0)
  infiniteScroll!.update()
return

eoj:
release
```
<br><br>
<div style="text-align: center;">
  <img style="border:thin solid var(--bbj-color-default);" src="assets/pull-to-refresh.gif" alt="BBjInfiniteScroll And BBjPullToRefresh">
</div>
<br><br>


## Contacts List Sample

The following sample , uses the ChileCompany customers table to build a paginated contacts list with 
infinity scrolling and pull to refresh.

```BBj
use ::BBjInfiniteScroll/BBjInfiniteScroll.bbj::BBjInfiniteScroll
use ::BBjInfiniteScroll/BBjInfiniteScroll.bbj::BBjInfiniteScrollPaginater
use ::BBjPullToRefresh/BBjPullToRefresh.bbj::BBjPullToRefresh
use com.basiscomponents.db.ResultSet
use com.basiscomponents.bc.SqlQueryBC
use java.nio.file.Files
use java.nio.file.Paths

wnd! = BBjAPI().openSysGui("X0").addWindow(10,10,300,400,"ChileCompany ContactsList")
fs! = BBjAPI().getFileSystem()
css! = new String(Files.readAllBytes(Paths.get(fs!.resolvePath("./ContactsList.css"))))
wnd!.setAttribute("@app-style-bottom", css!)

wnd!.setCallback(BBjAPI.ON_CLOSE,"eoj")

sql! = new SqlQueryBC(BBjAPI().getJDBCConnection("ChileCompany"))

infiniteScroll! = new BBjInfiniteScroll(wnd!)
infiniteScroll!.onScroll("onScroll")
infiniteScrollCanvas! = infiniteScroll!.getCanvas()

pullToRefresh! = new BBjPullToRefresh(infiniteScrollCanvas!, BBjPullToRefresh.PREPEND_SELF)
pullToRefresh!.onRefresh("onRefresh")

query! = "select count(*) as COUNT FROM CUSTOMER"
paginater! = new BBjInfiniteScrollPaginater()
paginater!.setTotalItems(sql!.retrieve(query!).getItem(0).getField("COUNT").getBigDecimal())
paginater!.setPageSize(10)

process_events

onScroll:
    startIndex! = paginater!.getStartIndex()
    endIndex! = paginater!.getEndIndex()

    if(paginater!.getEndIndex() > -1)
      query! = "SELECT * FROM CUSTOMER limit " + str(startIndex! + 1) + "," + str(endIndex! + 1)
      items! = sql!.retrieve(query!)
      iterator! = items!.iterator()

      while iterator!.hasNext()
          item! = iterator!.next()
          firstName! = item!.getField("FIRST_NAME").getString().trim()
          lastName! = item!.getField("LAST_NAME").getString().trim()
          fullName! = firstName! + " " + lastName!
          phone! = item!.getField("PHONE").getString().trim()
          country! = item!.getField("COUNTRY").getString().trim()
          city! = item!.getField("CITY").getString().trim()
          fullLocation! = country! + " - " + city!

          card! = infiniteScrollCanvas!.addChildWindow(infiniteScrollCanvas!.getAvailableControlID(), 0, 0, 0, 0, "", $00108800$, BBjAPI().getSysGui().getAvailableContext())
          card!.addStyle("bookEntry")

          avatarContent! = "<html><img src=""https://ui-avatars.com/api/?name=" + fullName! + "&&background=random"" /></html>"
          avatar! = card!.addStaticText(card!.getAvailableControlID(), 0, 0, 0, 0, avatarContent!)
          avatar!.addStyle("bookEntry__avatar")

          info! = card!.addChildWindow(card!.getAvailableControlID(), 0, 0, 0, 0, "", $00108800$, BBjAPI().getSysGui().getAvailableContext())
          info!.addStyle("bookEntry__info")

          name! = info!.addStaticText(info!.getAvailableControlID(), 0, 0, 0, 0, fullName!)
          name!.addStyle("bookEntry__name")

          location! = info!.addStaticText(info!.getAvailableControlID(), 0, 0, 0, 0, fullLocation!)
          location!.addStyle("bookEntry__location")

          call! = card!.addButton(card!.getAvailableControlID(), 0, 0, 0, 0, "<html><bbj-icon name=""phone""></bbj-icon></html>")
          call!.setEnabled(len(phone!) > 0)
          call!.setUserData(phone!)
          call!.setCallback(call!.ON_BUTTON_PUSH, "onCall")
      wend

      currentPage! = paginater!.getCurrentPage()
      paginater!.setCurrentPage(currentPage! + 1)
    fi

    if(endIndex! < 0 or endIndex! = (paginater!.getTotalItems() -1))
      infiniteScroll!.setCompleted(1)
    fi

    infiniteScroll!.update()
return

onRefresh:
  infiniteScroll!.setCompleted(1)
  infiniteScroll!.update()

  controls! = infiniteScrollCanvas!.getAllControls()
  size! = controls!.size() - 1
  for i = 0 to size!
    control! = controls!.get(i)
    if(control!.getID() <> pullToRefresh!.getID())
      control!.destroy()
    fi
  next i

  pullToRefresh!.finish()
  paginater!.setCurrentPage(1)
  infiniteScroll!.setCompleted(0)
  infiniteScroll!.update()
return

onCall:
  ev! = BBjAPI().getLastEvent()
  control! = ev!.getControl()
  phone! = str(control!.getUserData())
  script! = "" +
:    "(() => {" +
:    " const link = document.createElement('a');" +
:    " link.href='tel:" + phone! + "';" +
:    " link.style.visibility='hidden';" +
:    " document.body.appendChild(link);" +
:    " link.click();" +
:    " document.body.removeChild(link)" +
:    "})()"
  BBjAPI().getSysGui().executeScript(script!)
return

eoj:
release
```

```css
/** ContactsList.css **/
.bookEntry {
  display: flex;
  gap: 1rem;
  align-items: center;
  padding: var(--bbj-space-s);
  cursor: var(--bbj-cursor-click);
  transition: background-color var(--bbj-transition);
  border-bottom: thin solid var(--bbj-color-default);
  border-style: solid !important; 
}

.bookEntry:hover {
  background-color: var(--bbj-color-primary-alt);
}

.bookEntry__avatar {
  display: flex;
  align-items: center;
  width: var(--bbj-size-l);
  height: var(--bbj-size-l);
  border-radius: var(--bbj-border-radius-round);
}

.bookEntry__avatar img {
  width: 100%;
  height: 100%;
}

.bookEntry__info {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.bookEntry__location {
  font-size: var(--bbj-font-size-s);
  color: var(--bbj-color-default-text);
}
```

<br><br>
<div style="text-align: center;">
  <img style="border:thin solid var(--bbj-color-default);" src="assets/contacts-list.gif" alt="ChileCompany Customers List">
</div>
<br><br>