# Sorting Through Drupal Messages In Kibana Logs

Kibana is a great resource to visualize and filter logging data coming out of Express and Atlas. You can export graphs and share URLs with your teammates to help with bug tickets.

For this document, we will only be talking about messages coming out of Drupal, as the other messages are related to Atlas and its tech stack.

## Kibana Base URL

You can start out at https://osr-prod-log01.int.colorado.edu/app/kibana to view Kibana at its root. For now, click on the "Discover" tab in the right-hand sidebar, if you are not on it already.

![screen shot 2018-07-10 at 3 20 07 pm](https://user-images.githubusercontent.com/3640707/42538292-ce344a9a-8454-11e8-9baa-1171119fc143.png)

You should see a screen that looks something like that. It contains a few parts:
- **Search Bar:** This will have a placeholder of `Search... (e.g. status:200 AND extension:PHP)` and allows a fuzzy search of all the included fields.
- **Time Series Graph:** The count of the number of messages as time goes on. 
- **Filter Sidebar:** Allows selecting fields to filter by and also exclude.
- **Message Table:** A table of messages that you can expand to view each message's fields.

Those are the basic sections included in the Discover tab. Now let's go through some examples of debugging log messages to show what parts of these sections you might use and care about.

## A Basic Search

Say you have a particular message showing up on a site and you want to see if it appears on other sites or during which timeframes the message shows up. You can simply type the message in the search bar to find out.

For example, you might see something like this notice in the logs:
```
Notice: Undefined index: und in cu_block_section_preprocess_entity() (line 99 of /data/atlas/code/profiles/express/express-2.9.1/modules/features/cu_block_section/cu_block_section.module).
```

If you try to copy and paste that message into the search bar, then you'll see an error.

![screen shot 2018-07-10 at 3 33 02 pm](https://user-images.githubusercontent.com/3640707/42538927-9242bf1a-8456-11e8-8657-8ef8fa628a95.png)

Once you put quotes around the message, Kibana/Elastic Search will filter correctly; however, you'll probably want to use wildcard searches at some point...and we'll get to that later.

## Using The Filter Sidebar To Only See Drupal Messages

I usually start off with `*` in the searh bar in order to not filter anything out. Then, I go to the filter sidebar and select `filebeat-drupal_syslog-*` from the index pattern select list.

![screen shot 2018-07-10 at 3 38 43 pm](https://user-images.githubusercontent.com/3640707/42539152-5e8ef0d4-8457-11e8-98be-d49bd1f732a1.png)

I also select `drupal_message` to make the messages easier to read. Once you do that, you should see something like:

![screen shot 2018-07-10 at 3 43 23 pm](https://user-images.githubusercontent.com/3640707/42539418-5612a828-8458-11e8-9b99-c89a11410469.png)

### Excluding Messages That Are Already Known

Some messages repeat themselves at a high frequency, which makes other errors harder to see that need dealt with. This is where the "Filter out value" icons come in handy. You can filter out values, filter for values, and look for if the field is present.

For example, "CLEAR token found" is a message that you will see a lot but not want to scroll through. If you open up one of those messages, you'll see all of the fields that you can filter on. 

![screen shot 2018-07-10 at 3 56 17 pm](https://user-images.githubusercontent.com/3640707/42539964-f59e52ba-8459-11e8-9142-81e884c5573a.png)

For `drupal_category` you'll see `rave_alerts` listed. This comes from the `watchdog($name, $message)` function. In the Express code, that message is logged via `watchdog('rave_alerts', 'CLEAR token found');` in https://github.com/CuBoulder/express/blob/dev/modules/contrib/rave_alerts/rave_alerts.module#L138.

Since we don't care about this message right now, let's filter out all `rave_alerts` messages in `drupal_category`. Click on the "minus magnifying glass" that you see next to that part of the document. (Side note: each record is called a document in Kibana/Elastic Search). It will say "Filter out value" if you hover over it.

![screen shot 2018-07-10 at 4 17 53 pm](https://user-images.githubusercontent.com/3640707/42540736-ea34fb60-845c-11e8-9f58-f91ead072cbd.png)

You will now notice red ovals near the search bar...hey I don't know what else to call them...If you filter for values than the ovals will be gray-ish green. You can delete, edit, toggle things by hovering over the oval.

![screen shot 2018-07-10 at 4 22 21 pm](https://user-images.githubusercontent.com/3640707/42540904-82c0f0dc-845d-11e8-88a0-16b36e45f02d.png)

### Custom Query Syntax

You can add custom query syntax for filters that aren't provided by default. Searching for wildcards or regex-y type values, are some of the first things that come to mind. By clicking "Add a filter" and selecting a filter, you will be shown a list of "operators" you can use. There aren't many of them.

![screen shot 2018-07-10 at 4 28 58 pm](https://user-images.githubusercontent.com/3640707/42541122-669fc4f4-845e-11e8-8d74-6aec4be2abfe.png)

To add a custom filter, click "Edit Query DSL". You can read https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl.html to see all of what you can do with the DSL, but for a wildcard example, you can enter the following JSON.

```json
{
  "query": {
    "wildcard": {
      "drupal_message": "New*tokens."
    }
  }
}
```

This is a silly example, but you should see all of the "New RSS item found. Checking tokens." messages once you add that filter. In cases where you have `user johnny did something` and `johnny` is a variable, then `user*did something` should work to filter down to the messages you want to see.



