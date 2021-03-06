[![license](https://img.shields.io/github/license/StefanoBelli/telegram-bot-api.svg)](https://github.com/StefanoBelli/telegram-bot-api/blob/master/LICENSE)
[![GitHub release](https://img.shields.io/github/release/StefanoBelli/telegram-bot-api/all.svg)](https://github.com/StefanoBelli/telegram-bot-api/tags)
[![Travis](https://img.shields.io/travis/StefanoBelli/xxtelebot.svg)](https://travis-ci.org/StefanoBelli/xxtelebot)
**Bot API version: 3.3**

# xxtelebot: A simple C++11 Telegram Bot API implementation
C++11 Telegram Bot APIs

*This API wrapper tries to be more conformant as possible to Telegram Bot API, which you are invited to read.*

[Telegram Bot API](https://core.telegram.org/bots/api)

(C++11 compiler **ABSOLUTELY** needed)

### Compatability
This library is tested using Linux and OS X, with both GCC and Clang compilers.

Should work on BSD systems

High code complexity to make it work on MSVC compiler with Windows, but with some tricks it might be possible to make it work with MinGW, however I am not going to support Windows builds.

### Using CMake 
```
$ mkdir build && cd build
$ cmake .. -DCMAKE_BUILD_TYPE="Release" \
				[-DCMAKE_CXX_FLAGS="your optimizing compiler flags"] \
				[-DBUILD_SHARED_LIBS=ON] \
				[-DCMAKE_INSTALL_PREFIX:PATH="custom prefix path, should be /usr on GNU/Linux systems"] 
$ make
# make install
```

### Thanks to 
 
 * Deni, which tested some API methods, and proposed some examples! (He is listed in contributors)
	
 * @foxcpp, who given me a lot of advices for CMake! (issue #16)

 * @mandlm, PR #21, use this library within your CMake project

### Issues
You are welcome to open issues, do it without freaking out and/or insults, attach your code (take care of your **token**), and what is not going well. 

### CURL SSL/TLS backend
This library requires you provide the GnuTLS implementation for libcurl. Just get it from your package manager.

*Beware that if you use another backend, such as, OpenSSL, you won't get any error or warning! Linker just does its job and brings you the executable. To ensure that no race condition occour, you should use GnuTLS backend or NSS, which do not require us to specify a locking method.*

 * Arch Linux package: https://www.archlinux.org/packages/community/x86_64/libcurl-gnutls/

 * Debian package: https://packages.debian.org/search?keywords=libcurl4-gnutls-dev (the same applies for ubuntu, etc etc...)

 * Gentoo ebuild: https://packages.gentoo.org/packages/net-misc/curl (turn on the USEFLAG for gnutls)

 * Openmamba package: https://www.openmamba.org/distribution/distromatic.html?tag=devel&pkg=libcurl-gnutls.x86_64 This is the unique RPM package I found.

### Behaviour
 * With long poll bots, you have a connection always on to the telegram API endpoint, used to retrieve updates.
 * When an update is received, update gets dispatched and assigned to the default callback assigned, if no callback registered, update gets ignored forever.
 * A thread gets spawned and the callback actually called, then detached from program execution, it is expected to terminate when assigned callback ends
 * Your stuffs...

Another connection gets openen when using API methods, so this may slow down a little bit your bot experience.

Unfortunately this is the most safe way we have to avoid race conditions, phenomen caused by multithreading on shared CURL instance.

### Error reporting
Function will return surely (unless parsing errors of mine) the expected result.

If telegram API reports error, *TelegramException* gets thrown. So you may want to use try-catch blocks

### Signal handling
What we would like to have, when SIGINT (^C sequence) is sent to the program is to exit loop and follow cleanup operations. In this case, we cannot do this. Assuming to use a control variable, this control variable gets successfully changed when asynchronus signal is received, BUT, loop exits at the next iteration, not immediatly. Not our expected behaviour. Let the program die, the operating system will free previously allocated memory. (CURL resources)

### Example
A simple echo-back bot (long poll)

```c++
#include <string>
#include <tgbot/bot.h>

using namespace tgbot;
using namespace types;
using namespace methods;

void echoBack(const Message message, const Api& api) {
	api.sendMessage(std::to_string(message.chat.id), *message.text);
}

int main() {
	LongPollBot bot("token");
	//bot.callback(echoBack);
	bot.callback([](const Message m, const Api& a) {
	    a.sendMessage(std::to_string(m.chat.id),"replying for a C++ lambda!");
	});

	bot.start();
	
	//unreachable code
}
```

*I would suggest you to add a filter for updates, I mean, if your bot expects only messages, LongPollBot constructor allows you to add filters and get only certain update types*

#### How to properly compile your own bot

Assuming we call this file *bot.cpp* and it is placed one level up telegram-bot-api source tree, this would be its compilation command (GCC-C++):

~~~
$ g++ bot.cpp -Itelegram-bot-api/include telegram-bot-api/lib/libtgbot.a -lcurl -ljsoncpp -pthread
~~~

Others may result in linkage error.

### getUpdates
Is used within project implementation, and not "publicly" availible

### Documentation
Documentation is automatically generated by Doxygen and deployed by continuous integration tool after build succeed.

[TgBot documentation](http://stefanobelli.github.io/telegram-bot-api)

### Implementation status

 * Internal project utils [ directory: *include/tgbot/utils* ]
    
    * [x] HTTPS interface (libcurl wrapper)

 * Basic Bot interface [ files: *include/tgbot/bot.h* and *include/tgbot/register_callback.h* ]
    
    * [x] Bot class

    * [x] RegisterCallback class
    
 * API Types [ file: *include/tgbot/types.h* ]
 
    * [x]   User

	* [x]	ChatPhoto

	* [x]	MessageEntity
	
	* [x]	Audio
	
	* [x]	PhotoSize
	
	* [x]	Document
	
	* [x]	Voice
	
	* [x]	Contact
	
	* [x]	Location
	
	* [x]	Animation
	
	* [x]	Venue
	
	* [x]	VideoNote
	
	* [x]	MaskPosition
	
	* [x]	Sticker
	
	* [x]	StickerSet
	
	* [x]	Video
	
	* [x]	Invoice
	
	* [x]	ShippingAddress
	
	* [x]	OrderInfo
	
	* [x]	SuccessfulPayment
	
	* [x]	Game
	
	* [x]	Chat
	
	* [x]	Message
	
	* [x]	InlineQuery
	
	* [x]	ChosenInlineResult
	
	* [x]	CallbackQuery
	
	* [x]	ShippingQuery
	
	* [x]	PreCheckoutQuery
	
	* [x]	Update
	
	* [x]	ResponseParameters
	
	* [x]	File
	
	* [x]	UserProfilePhotos
	
	* [x]	KeyboardButton

	* [x]   ChatMember
	
 * API Methods and input types [ directory: *include/tgbot/methods* ]

	* [x]  ChatMemberRestrict 

	* [x]  ChatMemberPromote 

	* [x]  InlineKeyboardButton 

	* [x]  ReplyMarkup 

	* [x]  EmptyReplyMarkup 

	* [x]  InlineKeyboardMarkup 

	* [x]  ReplyKeyboardMarkup 

	* [x]  ReplyKeyboardRemove 

	* [x]  ForceReply 

	* [x]  InlineQueryResult 

	* [x]  InlineQueryResultAudio 

	* [x]  InlineQueryResultArticle 

	* [x]  InlineQueryResultContact 

	* [x]  InlineQueryResultGame 

	* [x]  InlineQueryResultDocument 

	* [x]  InlineQueryResultGif 

	* [x]  InlineQueryResultLocation 

	* [x]  InlineQueryResultMpeg4Gif 

	* [x]  InlineQueryResultPhoto 

	* [x]  InlineQueryResultVenue 

	* [x]  InlineQueryResultVideo 

	* [x]  InlineQueryResultVoice 
	
	* [x]  InlineQueryResultCachedAudio 

	* [x]  InlineQueryResultCachedDocument 

	* [x]  InlineQueryResultCachedGif 

	* [x]  InlineQueryResultCachedMpeg4Gif 

	* [x]  InlineQueryResultCachedPhoto 

	* [x]  InlineQueryResultCachedSticker 

	* [x]  InlineQueryResultCachedVideo 

	* [x]  InlineQueryResultCachedVoice 

	* [x]  InputMessageContent 

	* [x]  InputTextMessageContent 

	* [x]  InputLocationMessageContent 

	* [x]  InputContactMessageContent 

	* [x]  InputVenueMessageContent 

	* [x]  LabeledPrice 

	* [x]  Invoice 

	* [x]  ShippingOption 

 * API Methods [ directory: *include/tgbot/methods* ]

	* [x]  addStickerToSet

	* [x]  answerCallbackQuery

	* [x]  answerInlineQuery

	* [x]  answerPreCheckoutQuery

	* [x]  answerShippingQuery

	* [x]  createNewStickerSet

	* [x]  deleteChatPhoto

	* [x]  deleteMessage

	* [x]  deleteStickerFromSet

	* [x]  deleteWebhook

	* [x]  editMessageCaption

	* [x]  editMessageReplyMarkup

	* [x]  editMessageText

	* [x]  exportChatInviteLink

	* [x]  forwardMessage

	* [x]  getChat

	* [x]  getChatAdministrators

	* [x]  getChatMember

	* [x]  getChatMembersCount

	* [x]  getFile

	* [x]  getGameHighScores

	* [x]  getMe

	* [x]  getStickerSet

	* [x]  getUserProfilePhotos

	* [x]  getWebhookInfo

	* [x]  kickChatMember

	* [x]  leaveChat

	* [x]  pinChatMessage

	* [x]  promoteChatMember

	* [x]  restrictChatMember

	* [x]  sendAudio

	* [x]  sendChatAction

	* [x]  sendContact

	* [x]  sendDocument

	* [x]  sendGame

	* [x]  sendInvoice

	* [x]  sendLocation

	* [x]  sendMessage

	* [x]  sendPhoto

	* [x]  sendSticker

	* [x]  sendVenue

	* [x]  sendVideo

	* [x]  sendVideoNote

	* [x]  sendVoice

	* [x]  setChatDescription

	* [x]  setChatPhoto

	* [x]  setChatTitle

	* [x]  setGameScore

	* [x]  setStickerPositionInSet
	
	* [x]  setWebhook

	* [x]  unbanChatMember

	* [x]  unpinChatMessage

	* [x]  uploadStickerFile

### systemd service unit
You can find a template for the bot systemd service inside data/ directory.

Edit the unit template for your own bot and then:

~~~
# cp data/bot.service.template /usr/lib/systemd/system/bot.service
# systemctl daemon-reload
# systemctl enable bot.service
~~~

Your bot will be started at next reboot, systemd will get the control on your bot and you should look at logs and stop via journalctl and systemctl tools.

*Note that you may need to uncomment the Environmental config option under Service section, depending on your LD_LIBRARY_PATH value and where CMake decides to put the shared library.*

### The dark side of Inline Query answers

After we recieve our inline query, we have to answer it, done using *answerInlineQuery* method.

answerInlineQuery accepts the following **parameter**: *std::vector<::tgbot::types::Ptr<InlineQueryResult>> const&*

Because of project structure we have to use a smart pointer (**std::unique_ptr**), each specific result is a derived class of InlineQueryResult.

```c++
#include <tgbot/bot.h>
#include <tgbot/utils/make_ptr.h>

using namespace tgbot;
using namespace types;
using namespace methods;
using namespace methods::types;

int main() {
	//alias provided by project headers
	InlineQueryResultsVector results;
	
	//first result
	Ptr<InlineQueryResultPhoto> photo = utils::makePtr<InlineQueryResultPhoto>();
	photo->type = iqrTypePhoto; //also iqrTypeAudio, iqrType<type> exist
	photo->id = "unique_identifier";

	//put first result in the container
	results.push_back(std::move(photo));

	//repeat this each time it is required

	//use results with answerInlineQuery
}
```

### Multithreading
This library doesn't involve you in handling multiple threads, but remember that if you are using a shared resource (e.g. global variable), you may encounter race conditions when multiple threads try to access it. Lock accesses if needed.

 * http://en.cppreference.com/w/cpp/thread/lock_guard
 * http://en.cppreference.com/w/cpp/atomic/atomic
 * http://en.cppreference.com/w/cpp/thread/mutex

### Exception handling
Ensure you handle exceptions properly.

```
void echoBackCallback(...) {
	api.sendMessage(...); //may throw TelegramException, what(): Too Many Requests
}
```

This kind of exception should be handled in order to avoid program stop (SIGABRT).

No need to handle main thread ("bot API message fetch") exception. If it raises probably something bad is happening and bot should stop. 

Anyway you can't handle that to resume the bot normal execution

```c++
try {
	bot.start();
} catch(...) {
	//then what?
}
```

Please note:
 
 * tgbot::TelegramException is meaning of an error given by Telegram Bot API
 * std::runtime_error is meaning any other error (CURL)

### Logging
A logging facility is now provided by this library, and by default, it will log on stdout.

Unless specified, you'll get when the bot starts listening for events and only when errors (e.g. TelegramException_s_) are met.

You can:

 * Change the output stream (stdout by default)
 * Do not output anything at all
 * Change time formatting
 * Choose if log EACH update you get (useless in most cases, will only slow down your bot experience) [DISABLED BY DEFAULT]
 * Log from your callbacks (do not copy Logger, I do not reccomend to do this. Copying is viable only through constructor.)

Some examples:

 * Do not log anything

 This is achieved by setting the failbit of the ostream on.

```c++
#include <tgbot/bot.h>

//... fold

int main() {
	std::cout.setstate(std::ios::failbit);
	LongPollBot bot { "tok" };
	bot.start();

	return 0;
}
```

 * Log on stderr

```c++
#include <tgbot/bot.h>

//... fold

int main() {
	LongPollBot bot { "tok" };
	bot.getLogger().setStream(std::cerr);
	bot.start();

	return 0;
}
```

 * Log to file

```c++
#include <fstream>
#include <tgbot/bot.h>

//... fold

int main() {

	std::ofstream outfile("log.txt");

	LongPollBot bot { "tok" };
	bot.getLogger().setStream(outfile);
	bot.start();

	return 0;
}
```

 * Log any update and change date formatting
 
```c++
#include <fstream>
#include <tgbot/bot.h>

//... fold

int main() {

	std::ofstream outfile("log.txt");

	LongPollBot bot { "tok" };
	bot.getLogger().setStream(outfile);
	bot.getLogger().setDateFormat("%H:%M");
	bot.notifyEachUpdate(true);
	bot.start();

	return 0;
}
```

#### Logging from callbacks

You can log your own:

 * simple info/error log

```c++
void messageCallback(const Message m, const Api& api) {
	api.sendMessage(...);
	api.getLogger().info("sent message back!!");
	api.getLogger().error("something went wrong :/");

	//that's fine
}
```

However, if you want to, say, change the stream or date format, you have to perform a *const_cast* to *Logger&*

This is because we are getting the logger via a const instance of Api and we are using the const specifier member function version, then we are getting a const lvalue reference to Logger (const Logger&). 
 
```c++
void messageCallback(const Message m, const Api& api) {
	api.sendMessage(...);
	const_cast<Logger&>(api.getLogger()).setStream(std::cerr);
	api.getLogger().info("sent message back!!");
	api.getLogger().error("something went wrong :/");

	//remember to restore previous stream if you want to...
	//that's fine
}
```

**NOTE THAT IT WILL BE USER RESPONSIBILITY TO LOCK ALL OPERATIONS RELATED TO setStream() and setDateFormat() inside user-defined callbacks.**

Just use info() and error().

### CURL

If you want to use curl to let the bot able to perform some http requests, just don't call **curl_global_init()** and **curl_global_cleanup()**!!
