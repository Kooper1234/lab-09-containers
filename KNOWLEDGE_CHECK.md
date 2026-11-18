# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

1. Every file that is copied into an image is extractable in the public image, so people could steal my AWS credentials
2. The learner lab credentials we have are temporary and so the image would stop working when they expire

...

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

1. I ran the container without -e so the credentials did not exist inside it
2. get_atlas_settings() checks for MONGODB_ATLAS_URL first. It wasn’t there, so the function used AWS SSM instead
3. Boto3 found my aws credentials with -v
4. Then with those credentials it read the SSM parameter mongodb_credentials

...

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

Before: 5e2e949516e0
After: 1ff6732e653f

The image ID is a checksum of the whole image, so when I changed the description the whole image ID changed. But in doing this i kept the tag 0.1.0. This is a problem because someone that pulled or cited 0.1.0 expects tht image to stay the same, but now the same tag produces different results. To fix this, I should have used a new version when changing it like 0.1.1 in project.toml.