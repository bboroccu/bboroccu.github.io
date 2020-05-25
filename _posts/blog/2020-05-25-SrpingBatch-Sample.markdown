---
layout: post
title: SpringBatch Sample
author: 허재성
date:   2020-05-25 23:00:00 -0400
permalink: /SpringBatch
categories: blog
tags: SpringBatch
excerpt: SpringBatch
github: https://github.com/bboroccu/batchtest
image: /assets/img/post-images/spring_batch.png
imageAlt: SpringBatch
image-slider: /assets/img/post-images/slider-images/spring_batch_slider.png
---

## SpringBatch Sample
- Springbatch skip 처리 과정 정리
  샘플 코드로 정리한다.

  ```java
  @Bean(STEP_NAME)    
  public Step sampleStep1() {
    return stepBuilderFactory.get(STEP_NAME)
      .<SampleModel, SampleModel>chunk(CHUNK_SIZE)
      .reader(itemReader)
      .processor(processor())
      .writer(writer())
      .faultTolerant()
      .skip(SampleException.class)
      .skipLimit(CHUNK_SIZE)
      .listener(new SampleListener())
      .build();
  }
  ```
  위 코드에서 CHUNK_SIZE 는 3이고 skip 조건은 SampleException이 발생했을때 이다. 그리고 skipLimit는 skip이 limit만큼 발생하면 배치를 중지하는 설정이다. 
  데이터가 1~10번까지 있고 이중 3번과 7번에서 SampleException이 발생하면 
  

```verilog
2020-05-19 16:03:24.690  INFO 64159 --- [    Test worker] o.s.b.c.l.support.SimpleJobLauncher      : Job: [SimpleJob: [name=TestJob]] launched with the following parameters: [{random=620593}] 
2020-05-19 16:03:24.697  INFO 64159 --- [    Test worker] o.s.batch.core.job.SimpleStepHandler     : Executing step: [sampleStep1] 
2020-05-19 16:03:24.698  INFO 64159 --- [    Test worker] c.t.s.b.linstener.SampleListener         : [STEP] beforeStep(sampleStep1) 
2020-05-19 16:03:24.706  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 1 
2020-05-19 16:03:24.706  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 2 
2020-05-19 16:03:24.706  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 3 
2020-05-19 16:03:24.711  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 1 
2020-05-19 16:03:24.711  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 2 
2020-05-19 16:03:24.714  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 4 
2020-05-19 16:03:24.715  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 5 
2020-05-19 16:03:24.715  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 6 
2020-05-19 16:03:24.715  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 4 
2020-05-19 16:03:24.715  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 5 
2020-05-19 16:03:24.715  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 6 
2020-05-19 16:03:24.717  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 7 
2020-05-19 16:03:24.719  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 8 
2020-05-19 16:03:24.719  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 9 
2020-05-19 16:03:24.719  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 8 
2020-05-19 16:03:24.719  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 9 
2020-05-19 16:03:24.722  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 10 
2020-05-19 16:03:24.722  INFO 64159 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 10 
2020-05-19 16:03:24.724  INFO 64159 --- [    Test worker] c.t.s.b.linstener.SampleListener         : total : 10, success : 8, fail : 2 
2020-05-19 16:03:24.725  INFO 64159 --- [    Test worker] o.s.batch.core.step.AbstractStep         : Step: [sampleStep1] executed in 28ms 
2020-05-19 16:03:24.729  INFO 64159 --- [    Test worker] o.s.b.c.l.support.SimpleJobLauncher      : Job: [SimpleJob: [name=TestJob]] completed with the following parameters: [{random=620593}] and the following status: [COMPLETED] in 36ms
```

로그와 같이 될 것이라고 예상을 했지만 실제로는

```verilog
2020-05-19 16:04:28.727  INFO 64301 --- [    Test worker] o.s.b.c.l.support.SimpleJobLauncher      : Job: [SimpleJob: [name=TestJob]] launched with the following parameters: [{random=53975}] 
2020-05-19 16:04:28.733  INFO 64301 --- [    Test worker] o.s.batch.core.job.SimpleStepHandler     : Executing step: [sampleStep1] 
2020-05-19 16:04:28.734  INFO 64301 --- [    Test worker] c.t.s.b.linstener.SampleListener         : [STEP] beforeStep(sampleStep1) 
2020-05-19 16:04:28.742  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 1 
2020-05-19 16:04:28.742  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 2 
2020-05-19 16:04:28.742  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 3 
2020-05-19 16:04:28.745  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 1 
2020-05-19 16:04:28.746  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 2 
2020-05-19 16:04:28.747  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 1 
2020-05-19 16:04:28.748  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 2 
2020-05-19 16:04:28.750  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 4 
2020-05-19 16:04:28.751  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 5 
2020-05-19 16:04:28.751  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 6 
2020-05-19 16:04:28.751  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 4 
2020-05-19 16:04:28.751  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 5 
2020-05-19 16:04:28.751  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 6 
2020-05-19 16:04:28.754  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 7 
2020-05-19 16:04:28.754  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 8 
2020-05-19 16:04:28.755  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 9 
2020-05-19 16:04:28.755  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 8 
2020-05-19 16:04:28.755  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 9 
2020-05-19 16:04:28.757  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : processing id : 10 
2020-05-19 16:04:28.757  INFO 64301 --- [    Test worker] c.t.s.b.job.SampleJobConfiguration       : complete id : 10 
2020-05-19 16:04:28.759  INFO 64301 --- [    Test worker] c.t.s.b.linstener.SampleListener         : total : 10, success : 8, fail : 2 
2020-05-19 16:04:28.761  INFO 64301 --- [    Test worker] o.s.batch.core.step.AbstractStep         : Step: [sampleStep1] executed in 28ms 
2020-05-19 16:04:28.765  INFO 64301 --- [    Test worker] o.s.b.c.l.support.SimpleJobLauncher      : Job: [SimpleJob: [name=TestJob]] completed with the following parameters: [{random=53975}] and the following status: [COMPLETED] in 36ms
```

  와 같이 1,2,3 이 진행되다 3번에서 실패가 나니 다시 1,2가 실행이 되고 정상 처리가 되어 다음 4,5,6이 실행되고 7,8,9 실행이 될때 7,8,9가 실행이 된거처럼 보이지만 7에서 실패하여 7이 제외되고 다시 실행이되어 complete id는 8,9만 찍히게 됩니다. 전체 처리한 카운트는 동일하게 표시되지만 processor는 중복으로 호출하게 됩니다.
faultTolerant로 실패처리를 하는 과정에서 FaultTolerantStepBuilder.java에 보면 processorTransactional 이 기본 true로 되어 있는데 FaultTolerantChunkProcessor.java의 transform을 보면

```java
@Override protected Chunk<O> transform(final StepContribution contribution, Chunk<I> inputs) throws Exception {
  Chunk<O> outputs = new Chunk<>();
  @SuppressWarnings("unchecked")
  final UserData<O> data = (UserData<O>) inputs.getUserData();
  final Chunk<O> cache = data.getOutputs();
  final Iterator<O> cacheIterator = cache.isEmpty() ? null : new ArrayList<>(cache.getItems()).iterator();
  final AtomicInteger count = new AtomicInteger(0);
  // final int scanLimit = processorTransactional && data.scanning() ? 1 : 	// 0;
  for (final Chunk<I>.ChunkIterator iterator = inputs.iterator(); iterator.hasNext();)
  {
    final I item = iterator.next();
    RetryCallback<O, Exception> retryCallback = new RetryCallback<O, Exception>() {
      @Override
      public O doWithRetry(RetryContext context) throws Exception {
        Timer.Sample sample = BatchMetrics.createTimerSample();
        String status = BatchMetrics.STATUS_SUCCESS;
        O output = null;
        try {
          count.incrementAndGet();
          O cached = (cacheIterator != null && cacheIterator.hasNext()) ? cacheIterator.next() : null;
          if (cached != null && !processorTransactional) {
            output = cached;
          }
          else {
            output = doProcess(item);
            if (output == null) {
              data.incrementFilterCount();
            } else if (!processorTransactional && !data.scanning()) {
              cache.add(output);
            }
          }
        }
        catch (Exception e) {
          status = BatchMetrics.STATUS_FAILURE;
          if (rollbackClassifier.classify(e)) {
            // Default is to rollback unless the classifier
            // allows us to continue
            throw e;
          }
          else if (shouldSkip(itemProcessSkipPolicy, e, contribution.getStepSkipCount())) {
            // If we are not re-throwing then we should check if
            // this is skippable
            contribution.incrementProcessSkipCount();
            logger.debug("Skipping after failed process with no rollback", e);
            // If not re-throwing then the listener will not be
            // called in next chunk.
            callProcessSkipListener(item, e);
          }
          else {
            // If it's not skippable that's an error in
            // configuration - it doesn't make sense to not roll
            // back if we are also not allowed to skip
            throw new NonSkippableProcessException(
              "Non-skippable exception in processor.  Make sure any exceptions that do not cause a rollback are skippable.",
              e);
          }
        }
        finally {
          stopTimer(sample, contribution.getStepExecution(), "item.process", status, "Item processing");
        }
        if (output == null) {
          // No need to re-process filtered items
          iterator.remove();
        }
        return output;
      }
    };
    RecoveryCallback<O> recoveryCallback = new RecoveryCallback<O>() {
      @Override
      public O recover(RetryContext context) throws Exception {
        Throwable e = context.getLastThrowable();
        if (shouldSkip(itemProcessSkipPolicy, e, contribution.getStepSkipCount())) {
          iterator.remove(e);
          contribution.incrementProcessSkipCount();
          logger.debug("Skipping after failed process", e);
          return null;
        }
        else {
          if (rollbackClassifier.classify(e)) {
            // Default is to rollback unless the classifier
            // allows us to continue
            throw new RetryException("Non-skippable exception in recoverer while processing", e);
          }
          iterator.remove(e);
          return null;
        }
      }
    };
    O output = batchRetryTemplate.execute(retryCallback, recoveryCallback, new DefaultRetryState( 				getInputKey(item), rollbackClassifier));
    if (output != null) {
      outputs.add(output);
    }
    /* 		 * We only want to process the first item if there is a scan for a 		 * failed item. 		 */
    if (data.scanning()) {
      while (cacheIterator != null && cacheIterator.hasNext()) {
        outputs.add(cacheIterator.next());
      }
      // Only process the first item if scanning
      break;
    }
  }
  return outputs;
}
```

  Chunk 단위로 item을 가지고 와서 processorTransactional이 true 일 경우에는 35라인에서 cache에 넣지 않기 때문에 retry시 처음부터 다시 실행을 하게 되어 중복 처리가 된다. processorTransactional를 false로 하면 35라인에서 처리한 item에 대해서는 캐싱처리하기 때문에 28라인에서 캐싱된 item을 무시하게 된다.
결론으로

```java
@Bean(STEP_NAME)
public Step sampleStep1() {
  return stepBuilderFactory.get(STEP_NAME)
    .<SampleModel, SampleModel>chunk(CHUNK_SIZE)
    .reader(itemReader)
    .processor(processor())
    .writer(writer())
    .faultTolerant()
    .skip(SampleException.class)
    .skipLimit(CHUNK_SIZE)
    .processorNonTransactional()
    .listener(new SampleListener())
    .build();
}
```

  processorNonTransactional로 processorTransactional 를 false로 세팅하여 사용하면 원하는 결과를 얻을 수 있다.
