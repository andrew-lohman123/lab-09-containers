# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

Anyone who pulls a public image can read every file that was copied into it, so they would get my AWS keys and could use my account. Also, Learner Lab credentials expire when the session ends, so the copied keys would stop working and I would have to rebuild and push the image every time.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

`get_atlas_settings()` checks for `MONGODB_ATLAS_URL` first, but without `-e` it wasn't set in the container. So it used boto3 and the mounted AWS credentials to read the SSM parameter `mongodb_credentials` from Lab 07. That parameter has the Atlas URL, user, and password, which the code used to connect.

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

Before: `4092753f107b`. After: `08ac8a208cd6`. Someone who used `0.1.0` before could now pull a different image under the same name without knowing it, so their results might not match. I should have raised the version and built it as a new tag like `0.1.1` instead.