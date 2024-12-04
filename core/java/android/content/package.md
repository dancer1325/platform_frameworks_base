* == classes
  * -- for --
    * accessing data | device
    * publishing data | device
  * / main categories of APIs:
    * Content sharing (`android.content`)
      * uses
        * share content -- between -- application components
        * MOST important classes are
          * `android.content.ContentProvider` & `android.content.ContentResolver` -- for --
            * managing & publishing persistent data -- associated with an -- application
          * `android.content.Intent` & `android.content.IntentFilter` -- for --
            * delivering structured messages -- between -- DIFFERENT application components / allow components
              * -- initiate -- other components
              * return results
    * Package management (`android.content.pm`)
      * uses
        * access information -- about an -- Android package (`.apk`)
          * _Example:_ information about its activities, permissions, services, signatures, providers 
      * `android.content.pm.PackageManager`
        * MOST important class
    * Resource management (`android.content.res`)
      * uses
        * retrieve resource data -- associated with an -- application 
          * _Example:_ strings, drawables, media, and device configuration details
      * `android.content.res.Resources`
        * MOST important class
