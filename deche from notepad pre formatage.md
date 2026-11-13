

vibe coding instructions for sanabor : 

- don't make a repository layer rather just use prisma directly in the service 

- making animation or something everytime the patient move up in , ot necessarly like a celebration but something to feel seen , you can add notiifcation for guardians each time the user changes that journey stage 

you can add a section or a blogs section , where you can add all the docs and blogs of nassim about healin and getting better and all that bullshit
you can make a rag too, and add all her documentation and some others maybe so that when a client discuss with nassem he can site them , make a response based on them or link them even



## DuckDB

duck db : 

-- UI : 

-- 1. Install the extension from the community repository
INSTALL ui FROM community;

-- 2. Load the extension
LOAD ui;

-- 3. Start the UI web server
CALL start_ui();


-- install httpfs for s3 and json extensions in the log are in json format
INSTALL httpfs;
LOAD httpfs;
INSTALL json;
LOAD json;


-- Configure persistent AWS S3 credentials
CREATE OR REPLACE PERSISTENT SECRET s3_creds (
    TYPE s3,
    PROVIDER config,
    KEY_ID 'YOUR_AWS_ACCESS_KEY_ID',
    SECRET 'YOUR_AWS_SECRET_ACCESS_KEY',
    REGION 'us-east-1' -- replace with your bucket's region (e.g., eu-west-1, us-west-2)
);

-- Create a db file
ATTACH 'cloudfront_analytics.duckdb' AS analytics;

-- Test query directly against S3
SELECT * 
FROM read_ndjson(
    's3://cloudfront-logs-734587241111-eu-central-1-an/AWSLogs/734587241111/CloudFront/E11WUTME9QJ3MC/**/*.gz'
)
LIMIT 5;

-- If that works, create your table directly:

CREATE TABLE IF NOT EXISTS analytics.cloudfront_logs AS
    SELECT
      strptime("date" || ' ' || "time", '%Y-%m-%d %H:%M:%S') AS timestamp,
      "date"::DATE AS date,
      "time"::TIME AS time,
      "c-ip"::VARCHAR AS client_ip,
      "c-country"::VARCHAR AS client_country,
      "cs-uri-stem"::VARCHAR AS uri_stem,
      split_part("cs-uri-stem"::VARCHAR, '/', 2) AS project_name,
      "sc-status"::INTEGER AS status_code,
      "sc-bytes"::BIGINT AS bytes_sent,
      "sc-content-type"::VARCHAR AS content_type,
      "x-edge-result-type"::VARCHAR AS edge_result_type,
      "cs(Referer)"::VARCHAR AS referer,
      "cs(User-Agent)"::VARCHAR AS user_agent
    FROM read_ndjson(
      's3://cloudfront-logs-734587241111-eu-central-1-an/AWSLogs/734587241111/CloudFront/E11WUTME9QJ3MC/**/*.gz',
      ignore_errors = true
    );



## IDK


updateMany is generally a "heavier" operation in the underlying SQL than a primary-key update, so if you trynna update a single row and you re tempted to use updateMany to avoid an eror get thrown out, use update instead and catch that s probably better,

1. Database Throughput (Performance)
Your Code: If you have 5 media items, you send 5 updateMany queries. Each query has to find a row by ID and write to 3 columns (status, announcementId, order).

Split Approach: You send 1 updateMany query for the bulk work (status, announcementId) and then 5 update queries for a single column (order).

Why the split wins: Databases handle one large bulk write much more efficiently than multiple smaller writes. By moving the "heavy" data (linking the IDs and changing the status) into a single command, you reduce the overhead of the database engine's query planner and lock manager.





CHAT CODE : 
create = async (params: { schoolId: string; input: CreateAnnouncementReq }) => {
  const { schoolId, input } = params;

  return await prisma.$transaction(async (tx) => {
    // 1. Create the Announcement first (this is our primary goal)
    const announcement = await tx.annoucement.create({
      data: {
        description: input.description,
        schoolId,
      },
    });

    if (!input.media || input.media.length === 0) {
      return { announcement, failedCount: 0 };
    }

    // 2. Perform a single updateMany for all media at once to set the announcementId and status
    // Note: This doesn't handle 'order' individually yet
    const mediaIds = input.media.map(m => m.id);
    
    // We update the status and link them to the new announcement
    const updateResults = await tx.media.updateMany({
      where: {
        id: { in: mediaIds },
        // Ensure we only touch media belonging to the same school for safety
        schoolId: schoolId 
      },
      data: {
        status: "CONFIRMED",
        annoucementId: announcement.id,
      }
    });

    // 3. Since updateMany can't set unique 'order' values for each ID, 
    // we run individual updates ONLY for the ordering. 
    // These are wrapped in the same transaction so they are fast.
    const orderUpdates = input.media.map((m) =>
      tx.media.update({
        where: { id: m.id },
        data: { order: m.order },
      }).catch(() => null) // We catch individual errors to prevent transaction rollback
    );

    await Promise.all(orderUpdates);

    const successfulCount = updateResults.count;
    const failedCount = input.media.length - successfulCount;

    return { 
      announcementId: announcement.id, 
      failedCount 
    };
  });
};

###
YOURS : 
create = async (params: { schoolId: string; input: CreateAnnouncementReq }) => {

const { schoolId, input } = params;

return await prisma.$transaction(async (tx) => {

const createdAnnouncement = await tx.announcement.create({

data: {

description: input.description,

schoolId,

},

});



const mediaIds = input.media?.map((m) => m.id) ?? [];

const queries = mediaIds.map((mediaId) =>

tx.media.updateMany({

where: { id: mediaId },

data: {

status: MediaStatus.CONFIRMED,

annoucementId: createdAnnouncement.id,

order: input.media?.find((m) => m.id === mediaId)?.order,

},

}),

);



const resolvedQueries = await Promise.all(queries);



const succefulMediaUpdates = resolvedQueries.filter((q) => q.count > 0);

const failedCount = succefulMediaUpdates.length - (input.media?.length ?? 0);



return { failedCount };

});

};






## 