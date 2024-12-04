* intent
  * 👀== abstract description of an operation to be performed 👀
    * == passive data structure / hold an abstract description -- of an action to be -- performed
  * uses
    * `Context.startActivity(Intent)` -> launch an `android.app.Activity`
      * MOST relevant use
    * `android.content.Context.sendBroadcast(Intent)` -> send it -- to -- any interested `BroadcastReceiver` components
    * `android.content.Context.startService(Intent)` or `android.content.Context.bindService()` -- to communicate with a -- background `android.app.Service`
  * 👀MAIN properties 👀
    * `action` == general action -- to be -- performed
      * _Example:_ `ACTION_VIEW`, `ACTION_EDIT`, `ACTION_MAIN`
    * `data` == data -- to -- operate on
      * _Example:_ person record | contacts database
      * `android.net.Uri`
    * _Examples of action/data pairs:_
      * `ACTION_VIEW content://contacts/people/1`
        * == display information -- about the -- person / id "1"
      * `ACTION_DIAL content://contacts/people/1`
        * == display the phone dialer / filled in with the person 
      * `ACTION_EDIT tel:123`
        * == display the phone dialer / filled in with the given number
  * TODO:
 
 * <p>In addition to these primary attributes, there are a number of secondary
 * attributes that you can also include with an intent:</p>
 *
 * <ul>
 *     <li> <p><b>category</b> -- Gives additional information about the action
 *         to execute.  For example, {@link #CATEGORY_LAUNCHER} means it should
 *         appear in the Launcher as a top-level application, while
 *         {@link #CATEGORY_ALTERNATIVE} means it should be included in a list
 *         of alternative actions the user can perform on a piece of data.</p>
 *     <li> <p><b>type</b> -- Specifies an explicit type (a MIME type) of the
 *         intent data.  Normally the type is inferred from the data itself.
 *         By setting this attribute, you disable that evaluation and force
 *         an explicit type.</p>
 *     <li> <p><b>component</b> -- Specifies an explicit name of a component
 *         class to use for the intent.  Normally this is determined by looking
 *         at the other information in the intent (the action, data/type, and
 *         categories) and matching that with a component that can handle it.
 *         If this attribute is set then none of the evaluation is performed,
 *         and this component is used exactly as is.  By specifying this attribute,
 *         all of the other Intent attributes become optional.</p>
 *     <li> <p><b>extras</b> -- This is a {@link Bundle} of any additional information.
 *         This can be used to provide extended information to the component.
 *         For example, if we have a action to send an e-mail message, we could
 *         also include extra pieces of data here to supply a subject, body,
 *         etc.</p>
 * </ul>
 *
 * <p>Here are some examples of other operations you can specify as intents
 * using these additional parameters:</p>
 *
 * <ul>
 *   <li> <p><b>{@link #ACTION_MAIN} with category {@link #CATEGORY_HOME}</b> --
 *     Launch the home screen.</p>
 *   </li>
 *   <li> <p><b>{@link #ACTION_GET_CONTENT} with MIME type
 *     <i>{@link android.provider.Contacts.Phones#CONTENT_URI
 *     vnd.android.cursor.item/phone}</i></b>
 *     -- Display the list of people's phone numbers, allowing the user to
 *     browse through them and pick one and return it to the parent activity.</p>
 *   </li>
 *   <li> <p><b>{@link #ACTION_GET_CONTENT} with MIME type
 *     <i>*{@literal /}*</i> and category {@link #CATEGORY_OPENABLE}</b>
 *     -- Display all pickers for data that can be opened with
 *     {@link ContentResolver#openInputStream(Uri) ContentResolver.openInputStream()},
 *     allowing the user to pick one of them and then some data inside of it
 *     and returning the resulting URI to the caller.  This can be used,
 *     for example, in an e-mail application to allow the user to pick some
 *     data to include as an attachment.</p>
 *   </li>
 * </ul>
 *
 * <p>There are a variety of standard Intent action and category constants
 * defined in the Intent class, but applications can also define their own.
 * These strings use Java-style scoping, to ensure they are unique -- for
 * example, the standard {@link #ACTION_VIEW} is called
 * "android.intent.action.VIEW".</p>
 *
 * <p>Put together, the set of actions, data types, categories, and extra data
 * defines a language for the system allowing for the expression of phrases
 * such as "call john smith's cell".  As applications are added to the system,
 * they can extend this language by adding new actions, types, and categories, or
 * they can modify the behavior of existing phrases by supplying their own
 * activities that handle them.</p>
 *
 * <a name="IntentResolution"></a>
 * <h3>Intent Resolution</h3>
 *
 * <p>There are two primary forms of intents you will use.
 *
 * <ul>
 *     <li> <p><b>Explicit Intents</b> have specified a component (via
 *     {@link #setComponent} or {@link #setClass}), which provides the exact
 *     class to be run.  Often these will not include any other information,
 *     simply being a way for an application to launch various internal
 *     activities it has as the user interacts with the application.
 *
 *     <li> <p><b>Implicit Intents</b> have not specified a component;
 *     instead, they must include enough information for the system to
 *     determine which of the available components is best to run for that
 *     intent.
 * </ul>
 *
 * <p>When using implicit intents, given such an arbitrary intent we need to
 * know what to do with it. This is handled by the process of <em>Intent
 * resolution</em>, which maps an Intent to an {@link android.app.Activity},
 * {@link BroadcastReceiver}, or {@link android.app.Service} (or sometimes two or
 * more activities/receivers) that can handle it.</p>
 *
 * <p>The intent resolution mechanism basically revolves around matching an
 * Intent against all of the &lt;intent-filter&gt; descriptions in the
 * installed application packages.  (Plus, in the case of broadcasts, any {@link BroadcastReceiver}
 * objects explicitly registered with {@link Context#registerReceiver}.)  More
 * details on this can be found in the documentation on the {@link
 * IntentFilter} class.</p>
 *
 * <p>There are three pieces of information in the Intent that are used for
 * resolution: the action, type, and category.  Using this information, a query
 * is done on the {@link PackageManager} for a component that can handle the
 * intent. The appropriate component is determined based on the intent
 * information supplied in the <code>AndroidManifest.xml</code> file as
 * follows:</p>
 *
 * <ul>
 *     <li> <p>The <b>action</b>, if given, must be listed by the component as
 *         one it handles.</p>
 *     <li> <p>The <b>type</b> is retrieved from the Intent's data, if not
 *         already supplied in the Intent.  Like the action, if a type is
 *         included in the intent (either explicitly or implicitly in its
 *         data), then this must be listed by the component as one it handles.</p>
 *     <li> For data that is not a <code>content:</code> URI and where no explicit
 *         type is included in the Intent, instead the <b>scheme</b> of the
 *         intent data (such as <code>http:</code> or <code>mailto:</code>) is
 *         considered. Again like the action, if we are matching a scheme it
 *         must be listed by the component as one it can handle.
 *     <li> <p>The <b>categories</b>, if supplied, must <em>all</em> be listed
 *         by the activity as categories it handles.  That is, if you include
 *         the categories {@link #CATEGORY_LAUNCHER} and
 *         {@link #CATEGORY_ALTERNATIVE}, then you will only resolve to components
 *         with an intent that lists <em>both</em> of those categories.
 *         Activities will very often need to support the
 *         {@link #CATEGORY_DEFAULT} so that they can be found by
 *         {@link Context#startActivity Context.startActivity()}.</p>
 * </ul>
 *
 * <p>For example, consider the Note Pad sample application that
 * allows a user to browse through a list of notes data and view details about
 * individual items.  Text in italics indicates places where you would replace a
 * name with one specific to your own package.</p>
 *
 * <pre> &lt;manifest xmlns:android="http://schemas.android.com/apk/res/android"
 *       package="<i>com.android.notepad</i>"&gt;
 *     &lt;application android:icon="@drawable/app_notes"
 *             android:label="@string/app_name"&gt;
 *
 *         &lt;provider class=".NotePadProvider"
 *                 android:authorities="<i>com.google.provider.NotePad</i>" /&gt;
 *
 *         &lt;activity class=".NotesList" android:label="@string/title_notes_list"&gt;
 *             &lt;intent-filter&gt;
 *                 &lt;action android:name="android.intent.action.MAIN" /&gt;
 *                 &lt;category android:name="android.intent.category.LAUNCHER" /&gt;
 *             &lt;/intent-filter&gt;
 *             &lt;intent-filter&gt;
 *                 &lt;action android:name="android.intent.action.VIEW" /&gt;
 *                 &lt;action android:name="android.intent.action.EDIT" /&gt;
 *                 &lt;action android:name="android.intent.action.PICK" /&gt;
 *                 &lt;category android:name="android.intent.category.DEFAULT" /&gt;
 *                 &lt;data android:mimeType="vnd.android.cursor.dir/<i>vnd.google.note</i>" /&gt;
 *             &lt;/intent-filter&gt;
 *             &lt;intent-filter&gt;
 *                 &lt;action android:name="android.intent.action.GET_CONTENT" /&gt;
 *                 &lt;category android:name="android.intent.category.DEFAULT" /&gt;
 *                 &lt;data android:mimeType="vnd.android.cursor.item/<i>vnd.google.note</i>" /&gt;
 *             &lt;/intent-filter&gt;
 *         &lt;/activity&gt;
 *
 *         &lt;activity class=".NoteEditor" android:label="@string/title_note"&gt;
 *             &lt;intent-filter android:label="@string/resolve_edit"&gt;
 *                 &lt;action android:name="android.intent.action.VIEW" /&gt;
 *                 &lt;action android:name="android.intent.action.EDIT" /&gt;
 *                 &lt;category android:name="android.intent.category.DEFAULT" /&gt;
 *                 &lt;data android:mimeType="vnd.android.cursor.item/<i>vnd.google.note</i>" /&gt;
 *             &lt;/intent-filter&gt;
 *
 *             &lt;intent-filter&gt;
 *                 &lt;action android:name="android.intent.action.INSERT" /&gt;
 *                 &lt;category android:name="android.intent.category.DEFAULT" /&gt;
 *                 &lt;data android:mimeType="vnd.android.cursor.dir/<i>vnd.google.note</i>" /&gt;
 *             &lt;/intent-filter&gt;
 *
 *         &lt;/activity&gt;
 *
 *         &lt;activity class=".TitleEditor" android:label="@string/title_edit_title"
 *                 android:theme="@android:style/Theme.Dialog"&gt;
 *             &lt;intent-filter android:label="@string/resolve_title"&gt;
 *                 &lt;action android:name="<i>com.android.notepad.action.EDIT_TITLE</i>" /&gt;
 *                 &lt;category android:name="android.intent.category.DEFAULT" /&gt;
 *                 &lt;category android:name="android.intent.category.ALTERNATIVE" /&gt;
 *                 &lt;category android:name="android.intent.category.SELECTED_ALTERNATIVE" /&gt;
 *                 &lt;data android:mimeType="vnd.android.cursor.item/<i>vnd.google.note</i>" /&gt;
 *             &lt;/intent-filter&gt;
 *         &lt;/activity&gt;
 *
 *     &lt;/application&gt;
 * &lt;/manifest&gt;</pre>
 *
 * <p>The first activity,
 * <code>com.android.notepad.NotesList</code>, serves as our main
 * entry into the app.  It can do three things as described by its three intent
 * templates:
 * <ol>
 * <li><pre>
 * &lt;intent-filter&gt;
 *     &lt;action android:name="{@link #ACTION_MAIN android.intent.action.MAIN}" /&gt;
 *     &lt;category android:name="{@link #CATEGORY_LAUNCHER android.intent.category.LAUNCHER}" /&gt;
 * &lt;/intent-filter&gt;</pre>
 * <p>This provides a top-level entry into the NotePad application: the standard
 * MAIN action is a main entry point (not requiring any other information in
 * the Intent), and the LAUNCHER category says that this entry point should be
 * listed in the application launcher.</p>
 * <li><pre>
 * &lt;intent-filter&gt;
 *     &lt;action android:name="{@link #ACTION_VIEW android.intent.action.VIEW}" /&gt;
 *     &lt;action android:name="{@link #ACTION_EDIT android.intent.action.EDIT}" /&gt;
 *     &lt;action android:name="{@link #ACTION_PICK android.intent.action.PICK}" /&gt;
 *     &lt;category android:name="{@link #CATEGORY_DEFAULT android.intent.category.DEFAULT}" /&gt;
 *     &lt;data android:mimeType="vnd.android.cursor.dir/<i>vnd.google.note</i>" /&gt;
 * &lt;/intent-filter&gt;</pre>
 * <p>This declares the things that the activity can do on a directory of
 * notes.  The type being supported is given with the &lt;type&gt; tag, where
 * <code>vnd.android.cursor.dir/vnd.google.note</code> is a URI from which
 * a Cursor of zero or more items (<code>vnd.android.cursor.dir</code>) can
 * be retrieved which holds our note pad data (<code>vnd.google.note</code>).
 * The activity allows the user to view or edit the directory of data (via
 * the VIEW and EDIT actions), or to pick a particular note and return it
 * to the caller (via the PICK action).  Note also the DEFAULT category
 * supplied here: this is <em>required</em> for the
 * {@link Context#startActivity Context.startActivity} method to resolve your
 * activity when its component name is not explicitly specified.</p>
 * <li><pre>
 * &lt;intent-filter&gt;
 *     &lt;action android:name="{@link #ACTION_GET_CONTENT android.intent.action.GET_CONTENT}" /&gt;
 *     &lt;category android:name="{@link #CATEGORY_DEFAULT android.intent.category.DEFAULT}" /&gt;
 *     &lt;data android:mimeType="vnd.android.cursor.item/<i>vnd.google.note</i>" /&gt;
 * &lt;/intent-filter&gt;</pre>
 * <p>This filter describes the ability to return to the caller a note selected by
 * the user without needing to know where it came from.  The data type
 * <code>vnd.android.cursor.item/vnd.google.note</code> is a URI from which
 * a Cursor of exactly one (<code>vnd.android.cursor.item</code>) item can
 * be retrieved which contains our note pad data (<code>vnd.google.note</code>).
 * The GET_CONTENT action is similar to the PICK action, where the activity
 * will return to its caller a piece of data selected by the user.  Here,
 * however, the caller specifies the type of data they desire instead of
 * the type of data the user will be picking from.</p>
 * </ol>
 *
 * <p>Given these capabilities, the following intents will resolve to the
 * NotesList activity:</p>
 *
 * <ul>
 *     <li> <p><b>{ action=android.app.action.MAIN }</b> matches all of the
 *         activities that can be used as top-level entry points into an
 *         application.</p>
 *     <li> <p><b>{ action=android.app.action.MAIN,
 *         category=android.app.category.LAUNCHER }</b> is the actual intent
 *         used by the Launcher to populate its top-level list.</p>
 *     <li> <p><b>{ action=android.intent.action.VIEW
 *          data=content://com.google.provider.NotePad/notes }</b>
 *         displays a list of all the notes under
 *         "content://com.google.provider.NotePad/notes", which
 *         the user can browse through and see the details on.</p>
 *     <li> <p><b>{ action=android.app.action.PICK
 *          data=content://com.google.provider.NotePad/notes }</b>
 *         provides a list of the notes under
 *         "content://com.google.provider.NotePad/notes", from which
 *         the user can pick a note whose data URL is returned back to the caller.</p>
 *     <li> <p><b>{ action=android.app.action.GET_CONTENT
 *          type=vnd.android.cursor.item/vnd.google.note }</b>
 *         is similar to the pick action, but allows the caller to specify the
 *         kind of data they want back so that the system can find the appropriate
 *         activity to pick something of that data type.</p>
 * </ul>
 *
 * <p>The second activity,
 * <code>com.android.notepad.NoteEditor</code>, shows the user a single
 * note entry and allows them to edit it.  It can do two things as described
 * by its two intent templates:
 * <ol>
 * <li><pre>
 * &lt;intent-filter android:label="@string/resolve_edit"&gt;
 *     &lt;action android:name="{@link #ACTION_VIEW android.intent.action.VIEW}" /&gt;
 *     &lt;action android:name="{@link #ACTION_EDIT android.intent.action.EDIT}" /&gt;
 *     &lt;category android:name="{@link #CATEGORY_DEFAULT android.intent.category.DEFAULT}" /&gt;
 *     &lt;data android:mimeType="vnd.android.cursor.item/<i>vnd.google.note</i>" /&gt;
 * &lt;/intent-filter&gt;</pre>
 * <p>The first, primary, purpose of this activity is to let the user interact
 * with a single note, as decribed by the MIME type
 * <code>vnd.android.cursor.item/vnd.google.note</code>.  The activity can
 * either VIEW a note or allow the user to EDIT it.  Again we support the
 * DEFAULT category to allow the activity to be launched without explicitly
 * specifying its component.</p>
 * <li><pre>
 * &lt;intent-filter&gt;
 *     &lt;action android:name="{@link #ACTION_INSERT android.intent.action.INSERT}" /&gt;
 *     &lt;category android:name="{@link #CATEGORY_DEFAULT android.intent.category.DEFAULT}" /&gt;
 *     &lt;data android:mimeType="vnd.android.cursor.dir/<i>vnd.google.note</i>" /&gt;
 * &lt;/intent-filter&gt;</pre>
 * <p>The secondary use of this activity is to insert a new note entry into
 * an existing directory of notes.  This is used when the user creates a new
 * note: the INSERT action is executed on the directory of notes, causing
 * this activity to run and have the user create the new note data which
 * it then adds to the content provider.</p>
 * </ol>
 *
 * <p>Given these capabilities, the following intents will resolve to the
 * NoteEditor activity:</p>
 *
 * <ul>
 *     <li> <p><b>{ action=android.intent.action.VIEW
 *          data=content://com.google.provider.NotePad/notes/<var>{ID}</var> }</b>
 *         shows the user the content of note <var>{ID}</var>.</p>
 *     <li> <p><b>{ action=android.app.action.EDIT
 *          data=content://com.google.provider.NotePad/notes/<var>{ID}</var> }</b>
 *         allows the user to edit the content of note <var>{ID}</var>.</p>
 *     <li> <p><b>{ action=android.app.action.INSERT
 *          data=content://com.google.provider.NotePad/notes }</b>
 *         creates a new, empty note in the notes list at
 *         "content://com.google.provider.NotePad/notes"
 *         and allows the user to edit it.  If they keep their changes, the URI
 *         of the newly created note is returned to the caller.</p>
 * </ul>
 *
 * <p>The last activity,
 * <code>com.android.notepad.TitleEditor</code>, allows the user to
 * edit the title of a note.  This could be implemented as a class that the
 * application directly invokes (by explicitly setting its component in
 * the Intent), but here we show a way you can publish alternative
 * operations on existing data:</p>
 *
 * <pre>
 * &lt;intent-filter android:label="@string/resolve_title"&gt;
 *     &lt;action android:name="<i>com.android.notepad.action.EDIT_TITLE</i>" /&gt;
 *     &lt;category android:name="{@link #CATEGORY_DEFAULT android.intent.category.DEFAULT}" /&gt;
 *     &lt;category android:name="{@link #CATEGORY_ALTERNATIVE android.intent.category.ALTERNATIVE}" /&gt;
 *     &lt;category android:name="{@link #CATEGORY_SELECTED_ALTERNATIVE android.intent.category.SELECTED_ALTERNATIVE}" /&gt;
 *     &lt;data android:mimeType="vnd.android.cursor.item/<i>vnd.google.note</i>" /&gt;
 * &lt;/intent-filter&gt;</pre>
 *
 * <p>In the single intent template here, we
 * have created our own private action called
 * <code>com.android.notepad.action.EDIT_TITLE</code> which means to
 * edit the title of a note.  It must be invoked on a specific note
 * (data type <code>vnd.android.cursor.item/vnd.google.note</code>) like the previous
 * view and edit actions, but here displays and edits the title contained
 * in the note data.
 *
 * <p>In addition to supporting the default category as usual, our title editor
 * also supports two other standard categories: ALTERNATIVE and
 * SELECTED_ALTERNATIVE.  Implementing
 * these categories allows others to find the special action it provides
 * without directly knowing about it, through the
 * {@link android.content.pm.PackageManager#queryIntentActivityOptions} method, or
 * more often to build dynamic menu items with
 * {@link android.view.Menu#addIntentOptions}.  Note that in the intent
 * template here was also supply an explicit name for the template
 * (via <code>android:label="@string/resolve_title"</code>) to better control
 * what the user sees when presented with this activity as an alternative
 * action to the data they are viewing.
 *
 * <p>Given these capabilities, the following intent will resolve to the
 * TitleEditor activity:</p>
 *
 * <ul>
 *     <li> <p><b>{ action=com.android.notepad.action.EDIT_TITLE
 *          data=content://com.google.provider.NotePad/notes/<var>{ID}</var> }</b>
 *         displays and allows the user to edit the title associated
 *         with note <var>{ID}</var>.</p>
 * </ul>
 *
 * <h3>Standard Activity Actions</h3>
 *
 * <p>These are the current standard actions that Intent defines for launching
 * activities (usually through {@link Context#startActivity}.  The most
 * important, and by far most frequently used, are {@link #ACTION_MAIN} and
 * {@link #ACTION_EDIT}.
 *
 * <ul>
 *     <li> {@link #ACTION_MAIN}
 *     <li> {@link #ACTION_VIEW}
 *     <li> {@link #ACTION_ATTACH_DATA}
 *     <li> {@link #ACTION_EDIT}
 *     <li> {@link #ACTION_PICK}
 *     <li> {@link #ACTION_CHOOSER}
 *     <li> {@link #ACTION_GET_CONTENT}
 *     <li> {@link #ACTION_DIAL}
 *     <li> {@link #ACTION_CALL}
 *     <li> {@link #ACTION_SEND}
 *     <li> {@link #ACTION_SENDTO}
 *     <li> {@link #ACTION_ANSWER}
 *     <li> {@link #ACTION_INSERT}
 *     <li> {@link #ACTION_DELETE}
 *     <li> {@link #ACTION_RUN}
 *     <li> {@link #ACTION_SYNC}
 *     <li> {@link #ACTION_PICK_ACTIVITY}
 *     <li> {@link #ACTION_SEARCH}
 *     <li> {@link #ACTION_WEB_SEARCH}
 *     <li> {@link #ACTION_FACTORY_TEST}
 * </ul>
 *
 * <h3>Standard Broadcast Actions</h3>
 *
 * <p>These are the current standard actions that Intent defines for receiving
 * broadcasts (usually through {@link Context#registerReceiver} or a
 * &lt;receiver&gt; tag in a manifest).
 *
 * <ul>
 *     <li> {@link #ACTION_TIME_TICK}
 *     <li> {@link #ACTION_TIME_CHANGED}
 *     <li> {@link #ACTION_TIMEZONE_CHANGED}
 *     <li> {@link #ACTION_BOOT_COMPLETED}
 *     <li> {@link #ACTION_PACKAGE_ADDED}
 *     <li> {@link #ACTION_PACKAGE_CHANGED}
 *     <li> {@link #ACTION_PACKAGE_REMOVED}
 *     <li> {@link #ACTION_PACKAGE_RESTARTED}
 *     <li> {@link #ACTION_PACKAGE_DATA_CLEARED}
 *     <li> {@link #ACTION_PACKAGES_SUSPENDED}
 *     <li> {@link #ACTION_PACKAGES_UNSUSPENDED}
 *     <li> {@link #ACTION_UID_REMOVED}
 *     <li> {@link #ACTION_BATTERY_CHANGED}
 *     <li> {@link #ACTION_POWER_CONNECTED}
 *     <li> {@link #ACTION_POWER_DISCONNECTED}
 *     <li> {@link #ACTION_SHUTDOWN}
 * </ul>
 *
 * <p class="note"><strong>Note: </strong>If your app targets Android 11
 * (API level 30) or higher, registering broadcast such as
 * {@link #ACTION_PACKAGES_SUSPENDED} that includes package details in the
 * extras receives a filtered list of apps or nothing. Learn more about how to
 * <a href="/training/basics/intents/package-visibility">manage package visibility</a>.
 * </p>
 *
 * <h3>Standard Categories</h3>
 *
 * <p>These are the current standard categories that can be used to further
 * clarify an Intent via {@link #addCategory}.
 *
 * <ul>
 *     <li> {@link #CATEGORY_DEFAULT}
 *     <li> {@link #CATEGORY_BROWSABLE}
 *     <li> {@link #CATEGORY_TAB}
 *     <li> {@link #CATEGORY_ALTERNATIVE}
 *     <li> {@link #CATEGORY_SELECTED_ALTERNATIVE}
 *     <li> {@link #CATEGORY_LAUNCHER}
 *     <li> {@link #CATEGORY_INFO}
 *     <li> {@link #CATEGORY_HOME}
 *     <li> {@link #CATEGORY_PREFERENCE}
 *     <li> {@link #CATEGORY_TEST}
 *     <li> {@link #CATEGORY_CAR_DOCK}
 *     <li> {@link #CATEGORY_DESK_DOCK}
 *     <li> {@link #CATEGORY_LE_DESK_DOCK}
 *     <li> {@link #CATEGORY_HE_DESK_DOCK}
 *     <li> {@link #CATEGORY_CAR_MODE}
 *     <li> {@link #CATEGORY_APP_MARKET}
 *     <li> {@link #CATEGORY_VR_HOME}
 * </ul>
 *
 * <h3>Standard Extra Data</h3>
 *
 * <p>These are the current standard fields that can be used as extra data via
 * {@link #putExtra}.
 *
 * <ul>
 *     <li> {@link #EXTRA_ALARM_COUNT}
 *     <li> {@link #EXTRA_BCC}
 *     <li> {@link #EXTRA_CC}
 *     <li> {@link #EXTRA_CHANGED_COMPONENT_NAME}
 *     <li> {@link #EXTRA_DATA_REMOVED}
 *     <li> {@link #EXTRA_DOCK_STATE}
 *     <li> {@link #EXTRA_DOCK_STATE_HE_DESK}
 *     <li> {@link #EXTRA_DOCK_STATE_LE_DESK}
 *     <li> {@link #EXTRA_DOCK_STATE_CAR}
 *     <li> {@link #EXTRA_DOCK_STATE_DESK}
 *     <li> {@link #EXTRA_DOCK_STATE_UNDOCKED}
 *     <li> {@link #EXTRA_DONT_KILL_APP}
 *     <li> {@link #EXTRA_EMAIL}
 *     <li> {@link #EXTRA_INITIAL_INTENTS}
 *     <li> {@link #EXTRA_INTENT}
 *     <li> {@link #EXTRA_KEY_EVENT}
 *     <li> {@link #EXTRA_ORIGINATING_URI}
 *     <li> {@link #EXTRA_PHONE_NUMBER}
 *     <li> {@link #EXTRA_REFERRER}
 *     <li> {@link #EXTRA_REMOTE_INTENT_TOKEN}
 *     <li> {@link #EXTRA_REPLACING}
 *     <li> {@link #EXTRA_SHORTCUT_ICON}
 *     <li> {@link #EXTRA_SHORTCUT_ICON_RESOURCE}
 *     <li> {@link #EXTRA_SHORTCUT_INTENT}
 *     <li> {@link #EXTRA_STREAM}
 *     <li> {@link #EXTRA_SHORTCUT_NAME}
 *     <li> {@link #EXTRA_SUBJECT}
 *     <li> {@link #EXTRA_TEMPLATE}
 *     <li> {@link #EXTRA_TEXT}
 *     <li> {@link #EXTRA_TITLE}
 *     <li> {@link #EXTRA_UID}
 *     <li> {@link #EXTRA_USER_INITIATED}
 * </ul>
 *
 * <h3>Flags</h3>
 *
 * <p>These are the possible flags that can be used in the Intent via
 * {@link #setFlags} and {@link #addFlags}.  See {@link #setFlags} for a list
 * of all possible flags.
 */